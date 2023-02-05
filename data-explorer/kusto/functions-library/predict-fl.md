---
title: predict_fl() - Azure Data Explorer
description: This article describes the predict_fl() user-defined function in Azure Data Explorer.
ms.reviewer: adieldar
ms.topic: reference
ms.date: 11/08/2022
---
# predict_fl()

The function `predict_fl()` predicts using an existing trained machine learning model. This model was built using [Scikit-learn](https://scikit-learn.org/stable/), serialized to string, and saved in a standard Azure Data Explorer table.

> [!NOTE]
> * `predict_fl()` is a [UDF (user-defined function)](../query/functions/user-defined-functions.md). For more information, see [usage](#usage).
> * This function contains inline Python and requires [enabling the python() plugin](../query/pythonplugin.md#enable-the-plugin) on the cluster.

## Syntax

`T | invoke predict_fl(`*models_tbl*`,` *model_name*`,` *features_cols*`,` *pred_col*`)`

## Arguments

* *models_tbl*: The name of the table containing all serialized models. This table must contain the following columns:
    * *name*: the model name
    * *timestamp*: time of model training
    * *model*: string representation of the serialized model
* *model_name*: The name of the specific model to use.
* *features_cols*: Dynamic array containing the names of the features columns that are used by the model for prediction.
* *pred_col*: The name of the column that stores the predictions.

## Usage

`predict_fl()` is a user-defined [tabular function](../query/functions/user-defined-functions.md#tabular-function) to be applied using the [invoke operator](../query/invokeoperator.md). You can either embed its code as a query-defined function or you can create a stored function in your database. See the following tabs for more examples.

# [Query-defined](#tab/query-defined)

To use a query-defined function, embed the code using the [let statement](../query/letstatement.md). No permissions are required.

<!-- csl: https://help.kusto.windows.net/Samples -->
~~~kusto
let predict_fl=(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = bag_pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code = ```if 1:
        
        import pickle
        import binascii
        
        smodel = kargs["smodel"]
        features_cols = kargs["features_cols"]
        pred_col = kargs["pred_col"]
        bmodel = binascii.unhexlify(smodel)
        clf1 = pickle.loads(bmodel)
        df1 = df[features_cols]
        predictions = clf1.predict(df1)
        
        result = df
        result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])
        
    ```;
    samples
    | evaluate python(typeof(*), code, kwargs)
};
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
~~~

# [Stored](#tab/stored)

To store the function, see [`.create function`](../management/create-function.md). Creating a function requires [database user permission](../management/access-control/role-based-access-control.md).

### One-time installation

<!-- csl: https://help.kusto.windows.net/Samples -->
~~~kusto
.create function with (folder = "Packages\\ML", docstring = "Predict using ML model, build by Scikit-learn")
predict_fl(samples:(*), models_tbl:(name:string, timestamp:datetime, model:string), model_name:string, features_cols:dynamic, pred_col:string)
{
    let model_str = toscalar(models_tbl | where name == model_name | top 1 by timestamp desc | project model);
    let kwargs = bag_pack('smodel', model_str, 'features_cols', features_cols, 'pred_col', pred_col);
    let code = ```if 1:
        
        import pickle
        import binascii
        
        smodel = kargs["smodel"]
        features_cols = kargs["features_cols"]
        pred_col = kargs["pred_col"]
        bmodel = binascii.unhexlify(smodel)
        clf1 = pickle.loads(bmodel)
        df1 = df[features_cols]
        predictions = clf1.predict(df1)
        
        result = df
        result[pred_col] = pd.DataFrame(predictions, columns=[pred_col])
        
    ```;
    samples
    | evaluate python(typeof(*), code, kwargs)
}
~~~

### Usage

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
//
// Predicts room occupancy from sensors measurements, and calculates the confusion matrix
//
// Occupancy Detection is an open dataset from UCI Repository at https://archive.ics.uci.edu/ml/datasets/Occupancy+Detection+
// It contains experimental data for binary classification of room occupancy from Temperature,Humidity,Light and CO2.
// Ground-truth labels were obtained from time stamped pictures that were taken every minute
//
OccupancyDetection 
| where Test == 1
| extend pred_Occupancy=false
| invoke predict_fl(ML_Models, 'Occupancy', pack_array('Temperature', 'Humidity', 'Light', 'CO2', 'HumidityRatio'), 'pred_Occupancy')
| summarize n=count() by Occupancy, pred_Occupancy
```

---

Confusion matrix:
<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Occupancy	pred_Occupancy	n
TRUE	    TRUE	        3006
FALSE	    TRUE	        112
TRUE	    FALSE	        15
FALSE	    FALSE	        9284
```

### Model asset

Get sample dataset and pre-trained model in your cluster with Python plugin enabled.

<!-- csl -->
```kusto
//dataset
.set OccupancyDetection <| cluster('help').database('Samples').OccupancyDetection

//model
.set ML_Models <| datatable(name:string, timestamp:datetime, model:string) [
'Occupancy', datetime(now), '800363736b6c6561726e2e6c696e6561725f6d6f64656c2e6c6f6769737469630a4c6f67697374696352656772657373696f6e0a7100298171017d710228580700000070656e616c7479710358020000006c32710458040000006475616c7105895803000000746f6c7106473f1a36e2eb1c432d5801000000437107473ff0000000000000580d0000006669745f696e746572636570747108885811000000696e746572636570745f7363616c696e6771094b01580c000000636c6173735f776569676874710a4e580c00000072616e646f6d5f7374617465710b4e5806000000736f6c766572710c58090000006c69626c696e656172710d58080000006d61785f69746572710e4b64580b0000006d756c74695f636c617373710f58030000006f767271105807000000766572626f736571114b00580a0000007761726d5f737461727471128958060000006e5f6a6f627371134b015808000000636c61737365735f7114636e756d70792e636f72652e6d756c746961727261790a5f7265636f6e7374727563740a7115636e756d70790a6e6461727261790a71164b00857117430162711887711952711a284b014b0285711b636e756d70790a64747970650a711c58020000006231711d4b004b0187711e52711f284b0358010000007c71204e4e4e4affffffff4affffffff4b007471216289430200017122747123625805000000636f65665f7124681568164b008571256818877126527127284b014b014b05867128681c5802000000663871294b004b0187712a52712b284b0358010000003c712c4e4e4e4affffffff4affffffff4b0074712d628943286a02e0d50687e0bfc6d7c974fa93a63fb3d3b8080e6e943ffceb15defdad713f14c3a76bd73202bf712e74712f62580a000000696e746572636570745f7130681568164b008571316818877132527133284b014b01857134682b894308f1e89f57711290bf71357471366258070000006e5f697465725f7137681568164b00857138681887713952713a284b014b0185713b681c58020000006934713c4b004b0187713d52713e284b03682c4e4e4e4affffffff4affffffff4b0074713f628943040c00000071407471416258100000005f736b6c6561726e5f76657273696f6e71425806000000302e31392e32714375622e'
]
```
