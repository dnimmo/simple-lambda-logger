# simple-lambda-logger
Simple logging solution for Node.js AWS Lambda projects that supports multiple logging levels as well as 
optional support to send logs to third parties.

## Usage
```javascript
npm i @travel-cloud/simple-lambda-logger
```
Logging creation reads an ENV property to create a logger at the desired level, you can set this in AWS by simply setting the property as part of your configuration.

```javascript
process.env.LOG_LEVEL = 'DEBUG';

const logger = require('@travel-cloud/simple-lambda-logger');

mylogger = logger.newLogger();

mylogger.debug('Your logs');

```

By default logs are sent to the console in the format:

```javascript
'${timestamp} ${level} ${log}'
```

Any calls to log against a logger of a higher level resolve to an empty function and are ignored. For example the following has no effect:

```javaScript
process.env.LOG_LEVEL = 'ERROR';
const logger = require('@travel-cloud/simple-lambda-logger');

mylogger = logger.newLogger();

mylogger.debug('Your logs');
```


### Levels

1. DEBUG
2. INFO
3. ERROR

If no level property is specified or an unknown value is configured this will result in an error level logger being created.

## Third Parties

### logentries.com

If you would like to send your logs directly to logentries instead of the default AWS console you can do so by simply setting your token in the environment:

```javascript
process.env.LOGENTRIES_TOKEN = 'Your Token';
//then use the logger in the same way as normal
process.env.LOG_LEVEL = 'DEBUG';
const logger = require('@travel-cloud/simple-lambda-logger');
mylogger = logger.newLogger();
mylogger.debug('Your logs');
```

## Close
Usage of a third party logger in AWS Lambda requires you to close the simple logger. Failure to do so will most likely result in the function timing out as chances are the third party library is keeping its connection open. Using the simple logger you simply call close before the end of your lambda function:

```javascript
process.env.LOG_LEVEL = 'DEBUG';
const logger = require('@travel-cloud/simple-lambda-logger');
mylogger = logger.newLogger();
mylogger.debug('Your logs');
// terminate the logger
await mylogger.close();
//end the lambda function according to your requirements eg
callback(null, response);
```

Calling close returns a promise for you to await before terminating your lambda function, if the logger does not require closing a resolved promise is automatically returned.


# Dependencies

Logentries support is supplied by https://www.npmjs.com/package/le_node

