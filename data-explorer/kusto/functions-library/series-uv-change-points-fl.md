---
title: series_uv_change_points_fl() - Azure Data Explorer
description: This article describes the series_uv_change_points_fl() user-defined function in Azure Data Explorer.
ms.reviewer: adieldar
ms.topic: reference
ms.date: 11/08/2022
---
# series_uv_change_points_fl()

The function `series_uv_change_points_fl()` finds change points in time series by calling the [Univariate Anomaly Detection API](/azure/cognitive-services/anomaly-detector/overview), part of [Azure Cognitive Services](/azure/cognitive-services/what-are-cognitive-services). The function accepts a limited set of time series as numerical dynamic arrays, the change point detection threshold, and the minimum size of the stable trend window. Each time series is converted into the required JSON format and posts it to the Anomaly Detector service endpoint. The service response contains dynamic arrays of change points, their respective confidence, and the detected seasonality.

> [!NOTE]
>
> * This function is a [UDF (user-defined function)](../query/functions/user-defined-functions.md). For more information, see [usage](#usage).
> * Consider using the native function [series_decompose_anomalies()](../query/series-decompose-anomaliesfunction.md) which is more scalable and runs faster.

## Syntax

`T | invoke series_uv_change_points_fl(`*y_series* [`,` *score_threshold* [`,` *trend_window* [`,` *tsid*]]]`)`

## Arguments

| Name | Type | Required | Description |
|--|--|--|--|
| *y_series* | string | &check; | The name of the input table column containing the values of the series to be anomaly detected. |
| *score_threshold* | real | | A value specifying the minimum confidence to declare a change point. Each point whose confidence is above the threshold is defined as a change point. Default value: 0.9 |
| *trend_window* | integer | | A value specifying the minimal window size for robust calculation of trend changes. Default value: 5 |
| *tsid* | string | | The name of the input table column containing the time series ID. Can be omitted when analyzing a single time series. |

## Usage

`series_uv_change_points_fl()` is a user-defined [tabular function](../query/functions/user-defined-functions.md#tabular-function) applied using the [invoke operator](../query/invokeoperator.md). You can either embed its code in your query (ad hoc) or you can define it as a stored function in your database (persistent).

### Prerequisites

* An Azure subscription. Create a [free Azure account](https://azure.microsoft.com/free/).
* Access to a cluster and database. If necessary, create [a cluster and database](../../create-cluster-database-portal.md).
* [Enable the python() plugin](../query/pythonplugin.md#enable-the-plugin) on the cluster. This is necessary because this function contains inline Python.
* [Create an Anomaly Detector resource and obtain its key](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) to access the service.
* Enable the [http_request plugin / http_request_post plugin](../query/http-request-plugin.md) on the cluster to access the anomaly detection service endpoint.
* Modify the [callout policy](../management/calloutpolicy.md) for type `webapi` to access the anomaly detection service endpoint.

In the following function example, replace 'YOUR-AD-RESOURCE-NAME' in the uri and 'YOUR-KEY' in the 'Ocp-Apim-Subscription-Key' of the header with your resource name and key.

## [Ad hoc](#tab/adhoc)

For ad hoc usage, embed its code using the [let statement](../query/letstatement.md). No permissions are required.

<!-- csl: https://help.kusto.windows.net/Samples -->
~~~kusto
let series_uv_change_points_fl=(tbl:(*), y_series:string, score_threshold:real=0.9, trend_window:int=5, tsid:string='_tsid')
{
    let uri = 'https://YOUR-AD-RESOURCE-NAME.cognitiveservices.azure.com/anomalydetector/v1.0/timeseries/changepoint/detect';
    let headers=dynamic({'Ocp-Apim-Subscription-Key': h'YOUR-KEY'});
    let kwargs = bag_pack('y_series', y_series, 'score_threshold', score_threshold, 'trend_window', trend_window);
    let code = ```if 1:
        import json
        y_series = kargs["y_series"]
        score_threshold = kargs["score_threshold"]
        trend_window = kargs["trend_window"]
        json_str = []
        for i in range(len(df)):
            row = df.iloc[i, :]
            ts = [{'value':row[y_series][j]} for j in range(len(row[y_series]))]
            json_data = {'series': ts, "threshold":score_threshold, "stableTrendWindow": trend_window}     # auto-detect period, or we can force 'period': 84
            json_str = json_str + [json.dumps(json_data)]
        result = df
        result['json_str'] = json_str
    ```;
    tbl
    | evaluate python(typeof(*, json_str:string), code, kwargs)
    | extend _tsid = column_ifexists(tsid, 1)
    | partition by _tsid (
       project json_str
       | evaluate http_request_post(uri, headers, dynamic(null))
        | project period=ResponseBody.period, change_point=series_add(0, ResponseBody.isChangePoint), confidence=ResponseBody.confidenceScores
        | extend _tsid=toscalar(_tsid)
       )
}
;
let ts = range x from 1 to 300 step 1
| extend y=iff(x between (100 .. 110) or x between (200 .. 220), 20, 5)
| extend ts=datetime(2021-01-01)+x*1d
| extend y=y+4*rand()
| summarize ts=make_list(ts), y=make_list(y)
| extend sid=1;
ts
| invoke series_uv_change_points_fl('y', 0.8, 10, 'sid')
| join ts on $left._tsid == $right.sid
| project-away _tsid
| project-reorder y, *      //  just to visualize the anomalies on top of y series
| render anomalychart with(xcolumn=ts, ycolumns=y, confidence, anomalycolumns=change_point)
~~~

## [Persistent](#tab/persistent)

For persistent usage, use the [`.create function`](../management/create-function.md) to add the code to a stored function. Creating a function requires [database user permissions](../management/access-control/role-based-authorization.md).

### One time installation

<!-- csl: https://help.kusto.windows.net/Samples -->
~~~kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Time Series Change Points Detection by Azure Cognitive Service")
series_uv_change_points_fl(tbl:(*), y_series:string, score_threshold:real=0.9, trend_window:int=5, tsid:string='_tsid')
{
    let uri = 'https://YOUR-AD-RESOURCE-NAME.cognitiveservices.azure.com/anomalydetector/v1.0/timeseries/changepoint/detect';
    let headers=dynamic({'Ocp-Apim-Subscription-Key': h'YOUR-KEY'});
    let kwargs = bag_pack('y_series', y_series, 'score_threshold', score_threshold, 'trend_window', trend_window);
    let code = ```if 1:
        import json
        y_series = kargs["y_series"]
        score_threshold = kargs["score_threshold"]
        trend_window = kargs["trend_window"]
        json_str = []
        for i in range(len(df)):
            row = df.iloc[i, :]
            ts = [{'value':row[y_series][j]} for j in range(len(row[y_series]))]
            json_data = {'series': ts, "threshold":score_threshold, "stableTrendWindow": trend_window}     # auto-detect period, or we can force 'period': 84
            json_str = json_str + [json.dumps(json_data)]
        result = df
        result['json_str'] = json_str
    ```;
    tbl
    | evaluate python(typeof(*, json_str:string), code, kwargs)
    | extend _tsid = column_ifexists(tsid, 1)
    | partition by _tsid (
       project json_str
       | evaluate http_request_post(uri, headers, dynamic(null))
        | project period=ResponseBody.period, change_point=series_add(0, ResponseBody.isChangePoint), confidence=ResponseBody.confidenceScores
        | extend _tsid=toscalar(_tsid)
       )
}
~~~

### Usage example

<!-- csl: https://help.kusto.windows.net/Samples -->
~~~kusto
let ts = range x from 1 to 300 step 1
| extend y=iff(x between (100 .. 110) or x between (200 .. 220), 20, 5)
| extend ts=datetime(2021-01-01)+x*1d
| extend y=y+4*rand()
| summarize ts=make_list(ts), y=make_list(y)
| extend sid=1;
ts
| invoke series_uv_change_points_fl('y', 0.8, 10, 'sid')
| join ts on $left._tsid == $right.sid
| project-away _tsid
| project-reorder y, *      //  just to visualize the anomalies on top of y series
| render anomalychart with(xcolumn=ts, ycolumns=y, confidence, anomalycolumns=change_point)
~~~

---

The following graph shows change points on a time series.

![Graph showing change points on a time series.](images/series-uv-change-points-fl/uv-change-points-example-1.png)
