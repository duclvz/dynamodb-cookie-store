AWS DynamoDB Cookie Store API

Another NoSQL DynamoDB store for tough-cookie module.

## Installation
``` sh
$ npm install dynamodb-cookie-store --save
```

## Usage
``` js
var DynamoDBCookieStore = require('dynamodb-cookie-store');
var CookieJar = require('tough-cookie').CookieJar;
var AWS = require('aws-sdk');
AWS.config.update({
  region: 'us-west-2',
  endpoint: 'http://localhost:8000',
  // accessKeyId default can be used while using the downloadable version of DynamoDB. 
  // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
  accessKeyId: 'MyKeyId',
  // secretAccessKey default can be used while using the downloadable version of DynamoDB. 
  // For security reasons, do not store AWS Credentials in your files. Use Amazon Cognito instead.
  secretAccessKey: 'SecretAccessKey'
});

// Define an instance Store based on `key` property
var docClient = new AWS.DynamoDB.DocumentClient(); // client for DynamoDB
// DynamoDBCookieStore use the key `key` to manage cookie
var store = new DynamoDBCookieStore(docClient, 'example'); // key = 'example'
var jar = new CookieJar(store);

// By default DynamoDBCookieStore will use `cookie` table if not provide as third argument (tableName)
var store = new DynamoDBCookieStore(docClient, 'example', 'store'); // tableName = 'store'
// make sure your DynamoDB have that table named `store` to working properly

/* request example */
var request = require('request');
request = request.defaults({ jar : jar });
request('http://www.google.com', function() {
  request('http://images.google.com')
})

// Please create a Table `cookie` or whatever name you want to use this package!
// Remember that table should use the Primary partition key named as `key`
// Example createTable by JS code or you can do this by Create Table GUI on DynamoDB Web Console
var DynamoDB = new AWS.DynamoDB();
DynamoDB.createTable({
  TableName: "cookie",
  KeySchema: [
    { AttributeName: "key", KeyType: "HASH" }
  ],
  AttributeDefinitions: [
    { AttributeName: "key", AttributeType: "S" }
  ],
  ProvisionedThroughput: {
    ReadCapacityUnits: 5,
    WriteCapacityUnits: 5
  }
}, function (err, data) {
  if (err) {
    console.log("Unable to create table: " + "\n" + JSON.stringify(err, undefined, 2));
  }
  else {
    console.log("Created table: " + "\n" + JSON.stringify(data, undefined, 2));
  }
})

```

## License
MIT

