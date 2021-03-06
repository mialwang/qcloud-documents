## 1. API Description
This API (AddCdbParamTemplate) is used to add a parameter template of the Cloud Database instance.
You can also use API [Query List of Parameter Templates](/doc/api/253/7185) to query the details of the list of parameter templates.
Domain for API request: <font style='color:red'>cdb.api.qcloud.com </font>


## 2. Input Parameters
The following request parameter list only provides API request parameters. Common request parameters need to be added when the API is called. For more information, refer to <a href='/document/product/236/6921' title='Common Request Parameters'>Common Request Parameters</a>. The Action field for this API is AddCdbParamTemplate.

| Parameter Name | Required  | Type | Description |
|---------|---------|---------|---------|
| name | Yes |String |Template name, which must be unique.
| desc |No | String | Template description |
| engineVersion | No | String | Database version number. Possible returned values include: 5.1, 5.5, and 5.6; if templateId field is not uploaded, this value is required.  |
| templateId | No | Int | template ID; if the value is uploaded, the paramList field will be invalid.  |
| paramList.n | No | String | Parameter list. Format: paramList.0.name=auto_increment_increment&paramList.0.value=1 |


## 3. Output Parameters
| Parameter Name | Type | Description |
|---------|---------|---------|
| code | Int | Common error code; 0: Succeeded; other values: Failed. For more information, please refer to <a href='https://cloud.tencent.com/doc/api/372/%E9%94%99%E8%AF%AF%E7%A0%81#1.E3.80.81.E5.85.AC.E5.85.B1.E9.94.99.E8.AF.AF.E7.A0.81' title='Common Error Codes'>Common Error Codes</a> on the Error Code page. |
| message | String | Module error message description depending on API. |
| codeDesc | String | Error description |
| data | Array | Returned data |
Parameter data is composed of the following parameters:

| Parameter Name | Type | Description |
|---------|---------|---------|
| templateId | Int | Template ID | 


## 4. Error Codes
The following error codes only include the business logic error codes for this API.

| Error Code | Error Message | Error Description |
|---------|---------|---------|
| 9612 | InternalError | Database insert error |
| 9613 | InternalError | Database query error |
| 20001 | InternalError.ReachedMaximum | The maximum number of parameter templates is reached |
| 20002 | InvalidParameter.DuplicateName | A duplicate parameter template name exists |


## 5. Example
Input
<pre>
https://cdb.api.qcloud.com/v2/index.php?Action=AddCdbParamTemplate
&<<a href="/document/product/236/6921">Common request parameters</a>>
&engineVersion=5.6
&name=test
&amp;paramList.0.name=connect_timeout
&amp;paramList.0.value=10
</pre>

Output
```
{
    "code":"0",
    "message":"",
    "codeDesc":"Success",
    "data":{
        "templateId":"1"
    }
}
```


