[![Falkonry Logo](http://static1.squarespace.com/static/55a7df64e4b09f03368a7a78/t/569c6441ab281050fe32c18a/1453089858079/15-logo-transparent-h.png?format=500w)](http://falkonry.com/)

[![npm package](https://nodei.co/npm/falkonry-js-client.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/falkonry-js-client/)
[![Build status](https://img.shields.io/travis/Falkonry/falkonry-js-client.svg?style=flat-square)](https://travis-ci.org/Falkonry/falkonry-js-client)

Falkonry Javascript Client to access [Falkonry Condition Prediction](falkonry.com) APIs

## Installation

```bash
$ npm install falkonry-js-client
```

## Features

    * Create Eventbuffer
    * Retrieve Eventbuffers
    * Create Pipeline
    * Retrieve Pipelines
    * Add data to Eventbuffer (csv/json, stream)
    * Retrieve output of Pipeline
    * Create/update/delete subscription for Eventbuffer
    * Create/update/delete publication for Pipeline
    
## Quick Start

    * To create Eventbuffer
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var Schemas    = require('falkonry-js-client').Schemas;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');

var eventbuffer = new Schemas.Eventbuffer();
eventbuffer.setName('Test-Eeventbuffer-01');

var options = {
  'timeIdentifier' : 'time',
  'timeFormat'     : 'iso_8601'
};

return falkonry.createEventbuffer(eventbuffer, options, function(error, response){});
```

    * To get all Eventbuffers
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');
        
falkonry.getEventbuffers(function(error, pipelines){});
```

    * To create Pipeline
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var Schemas    = require('falkonry-js-client').Schemas;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');

var eventbuffer = new Schemas.Eventbuffer();
eventbuffer.setName('Test-Eeventbuffer-01');

var options = {
  'timeIdentifier' : 'time',
  'timeFormat'     : 'iso_8601'
};

return falkonry.createEventbuffer(eventbuffer, options, function(error, response){
    var pipeline = new Schemas.Pipeline();
    var signals  = {
      'current'   : 'Numeric',
      'vibration' : 'Numeric',
      'state'     : 'Categorical'
    };
    var assessment = new Schemas.Assessment();
    assessment.setName('Health')
        .setInputSignals(['current', 'vibration', 'state']);

    pipeline.setName('Pipeline-01')
        .setEventbuffer(response.getId())
        .setInputSignals(signals)
        .setThingName('Motor')
        .setAssessment(assessment);
    return falkonry.createPipeline(pipeline, function(error, response){});
});
```

    * To get all Pipelines
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');
        
falkonry.getPipelines(function(error, pipelines){});
```

    * To add json data to Eventbuffer
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');
var data = '{"time" :"2016-03-01 01:01:01", "current" : 12.4, "vibration" : 3.4, "state" : "On"}';
return falkonry.addInput('eventbufferId', 'json', data, function(error, response){});
```

    * To add csv data to Eventbuffer
    
```js
var Falkonry   = require('falkonry-js-client').Client;
var falkonry   = new Falkonry('https://service.falkonry.io', 'auth-token');
var data = 'time,current,vibration,state\n2016-03-01 01:01:01,12.4,3.4,On';
return falkonry.addInput('eventbufferId', 'csv', data, function(error, response){});
```

    * To add data from a stream to Eventbuffer
    
```js
var Falkonry = require('falkonry-js-client').Client;
var falkonry = new Falkonry('https://service.falkonry.io', 'auth-token');
var stream   = fs.createReadStream('/tmp/sample.json');

var streamHandler = falkonry.addInputFromStream('eventbuffer_id', 'json', stream, function(error, response){});
```

    * To get output of a Pipeline
    
```js
var Falkonry = require('falkonry-js-client').Client;
var falkonry = new Falkonry('https://service.falkonry.io', 'auth-token');
var stream   = fs.createReadStream('/tmp/sample.json');
var startTime = '1457018017'; //seconds since unix epoch 
var endTime   = '1457028017'; //seconds since unix epoch
var streamHandler = falkonry.getOutput('pipeline_id', startTime, endTime, function(error, stream){
    stream.pipe(fs.createWriteStream('/tmp/pipeline_output.json'));
});
```

    * To add subscription to an Eventbuffer
    
```js
var Falkonry = require('falkonry-js-client').Client;
var Schemas  = require('falkonry-js-client').Schemas;
var falkonry = new Falkonry('https://service.falkonry.io', 'auth-token');
var subscription = new Schemas.Subscription()
    .setType('MQTT')
    .setPath('mqtt://test.mosquito.com')
    .setTopic('falkonry-eb-1-test')
    .setUsername('test-user')
    .setPassword('test')
    .setTimeFormat('YYYY-MM-DD HH:mm:ss')
    .setTimeIdentifier('time');

return falkonry.createSubscription('eventbuffer_id', subscription, function(error, response){});
```


    * To add publication to a Pipeline
    
```js
var Falkonry = require('falkonry-js-client').Client;
var Schemas  = require('falkonry-js-client').Schemas;
var falkonry = new Falkonry('https://service.falkonry.io', 'auth-token');
var publication = new Schemas.Publication()
    .setType('MQTT')
    .setTopic('falkonry-test-pipeline')
    .setPath('mqtt://test.mosquito.com')
    .setUsername('test-user')
    .setPassword('test-password')
    .setContentType('application/json');
return falkonry.createPublication('pipeline_id', publication, function(error, response){});
```

## Docs

    * [Falkonry APIs](https://service.falkonry.io/api)
     
## Tests

  To run the test suite, first install the dependencies, then run `npm test`:
  
```bash
$ npm install
$ npm test
```

## License

  Available under [MIT License(LICENSE)
