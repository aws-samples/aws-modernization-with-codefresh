+++
title = "Creating a Lambda function"
chapter = false
weight = 21
+++

The first step of creating a lambda function is choosing a supported programming
language and write the method. Amazon Lambda supports natively

1. Java
1. Go
1. Node.js
1. C#
1. Python
1. Ruby
1. PowerShell 

In our case we will use Node/Javascript for the source code. 

You can find the example application at [https://github.com/codefresh-contrib/aws-workshop-demos/tree/main/lambda/aws_lambda_functions/simple](https://github.com/codefresh-contrib/aws-workshop-demos/tree/main/lambda/aws_lambda_functions/simple).


Our lambda function is very simple:

```javascript
module.exports.handler = async (event) => {
    console.log('Event: ', event);
    let responseMessage = 'Hello World from AWS Lambda';
  
    return {
      statusCode: 200,
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        message: responseMessage,
      }),
    }
  }
```

This is a very simple method that just prints a message when it is called.



