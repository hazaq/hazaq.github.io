---
layout: post
title:  "AWS Lambda with python"
date:   2019-04-12 19:52:00 +0400
categories: AWS
---

## Lambda
**Handler**:  
Lambda Function Handler is a function in your code, that AWS Lambda can invoke when the service executes your code.  
```
def handler_name(event, context): 
    ...
    return some_value
```
event – AWS Lambda uses this parameter to pass in event data to the handler. This parameter is usually of the Python dict type.  
context – AWS Lambda uses this parameter to provide runtime information to your handler. This parameter is of the LambdaContext type.  
Context Methods-  
```get_remaining_time_in_millis``` – Returns the number of milliseconds left before the execution times out.
Context Properties-  
function_name – The name of the Lambda function.  
function_version – The version of the function.  
invoked_function_arn – The Amazon Resource Name (ARN) used to invoke the function. Indicates if the invoker specified a version number or alias.  
memory_limit_in_mb – The amount of memory configured on the function.  
aws_request_id – The identifier of the invocation request.  
log_group_name – The log group for the function.  
log_stream_name – The log stream for the function instance.  
  
**Invocation Types**:  
AWS Lambda supports synchronous and asynchronous invocation of a Lambda function.  


