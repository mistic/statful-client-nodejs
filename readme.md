# Statful Client for NodeJS

[![NPM version][npm-image]][npm-url] [![Build Status](https://travis-ci.org/statful/statful-client-nodejs.svg?branch=master)](https://https://travis-ci.org/statful/statful-client-nodejs)

Staful client for NodeJS written in Javascript. This client is intended to gather metrics and send them to Statful.

## Table of Contents

* [Supported Versions of NodeJS](#supported-versions-of-nodejs)
* [Installation](#installation)
* [Quick Start](#quick-start)
* [Examples](#examples)
* [Reference](#reference)
* [Still Need Help?](#still-need-help)
* [Authors](#authors)
* [License](#license)

## Supported Versions of NodeJS

| Statful client Version | Tested NodeJS versions  |
|:---|:---|
| 1.0.x | 0.10, 0.12, 4, _Stable_ |

## Installation

```bash
$ npm install statful-client --save
```

## Quick start

After installing Statful Client you are ready to use it. The quickest way is to do the following:

```javascript
var Statful = require('statful-client');

// Creates an object with the configuration and pass it to the client
var config = {
    token: 'MyAppToken',
    app: 'AccountService',
    tags: { cluster: 'production' }
};
var statful = new Statful(config);

// Send a metric
statful.counter('transactions', 1);
```

> **IMPORTANT:** This configuration uses the default **host** and **port**. You can learn more about configuration in [Reference](#reference).

## Examples

You can find here some useful usage examples of the Statful Client. In the following examples is assumed you have already installed and included Statful Client in your project.

### UDP Configuration

Creates a simple UDP configuration for the client.

```javascript
var Statful = require('statful-client');

var config = {
    app: 'AccountService'
    transport: 'udp',
    host: 'statful-relay.yourcompany.com',
    tags: { cluster: 'production' },
};

var statful = new Statful(config);
```

### HTTP Configuration

Creates a simple HTTP API configuration for the client.

```javascript
var Statful = require('statful-client');

var config = {
    app: 'AccountService'
    transport: 'http',
    api: {
        token: 'YOUR_TOKEN_FOR_STATFUL_API'
    }
    tags: { cluster: 'production' },
};

var statful = new Statful(config);
```

### Logger configuration

Creates a simple client configuration and adds your favourite logger to the client, like Bunyan or Winston.

```javascript
var Statful = require('statful-client');
var logger = require('your-favourite-logging-lib');

var config = {
    app: 'AccountService'
    transport: 'http',
    api: {
        token: 'YOUR_TOKEN_FOR_STATFUL_API'
    },
    tags: { cluster: 'production' },
};

var statful = new Statful(config, logger);
```

### Defaults Configuration Per Method

Creates a configuration for the client with custom default options per method.

```javascript
var Statful = require('statful-client');

var config = {
    default: {
        counter: { agg: ['avg'], aggFreq: 180 },
        gauge: { agg: ['first'], aggFreq: 180 },
        timer: { tags: { cluster: 'qa' }, agg: ['count'], aggFreq: 180 }
    },
    tags: { cluster: 'production' },
    api: {
        token: 'YOUR_TOKEN_FOR_STATFUL_API'
    },
    transport: 'http'
}

var statful = new Statful(config);
```

### Mixed Complete Configuration

Creates a configuration defining a value for every available option.

``javascript
vr Statful = require('statful-client');

var config = {
    default: {
        timer: { tags: { cluster: 'qa' }, agg: ['count'], aggFreq: 180 }
    },
    dryRyn: true,
    flushInterval: 5000,
    flushSize: 50,
    transport: 'http',
    api: {
        secure: false,
        timeout: 300,
        token: 'YOUR_TOKEN_FOR_STATFUL_API'
    }
    namespace: 'application',
    tags: { cluster: 'production' }
}

var statful = new Statful(config);
```

### Add metrics

Creates a simple client configuration and use it to send some metrics.

```javascript
va Statful = require('statful-client');

var config = {
    app: 'AccountService'
    transport: 'udp',
    host: 'statful-relay.yourcompany.com',
    tags: { cluster: 'production' },
};

var statful = new Statful(config);

// Send three different metrics (gauge, timer and a counter)
statful.gauge('testGauge', 10);
statful.timer('testTimer', 100);
statful.counter('testCounter', 1, { agg: ['first'], aggFreq: 60, namespace: 'sandbox' });

// Metric to be sent with tags
statful.counter('testCounter', 1, {tags: {host, 'localhost', status: 'SUCCESS'}});
```

## Reference

Detailed reference if you want to take full advantage from Statful.

### Global configuration

The custom options that can be set on config param are detailed below.

| Option | Description | Type | Default | Required |
|:---|:---|:---|:---|:---|
| _app_ | Defines the application global name. If specified sets a global tag `app=setValue`. | `string` | **none** | **NO** |
| _default_ | Object to set methods options. | `object` | `{}` | **NO** |
| _api_ | Defined API configurations. | `object` | **none** | **NO** |
| _dryRun_ | Defines if metrics should be output to the logger instead of being send. | `boolean` | `false` | **NO** |
| _flushInterval_ | Defines the periodicity of buffer flushes in **miliseconds**. | `number` | `10000` | **NO** |
| _flushSize_ | Defines the maximum buffer size before performing a flush. | `number` | `10` | **NO** |
| _namespace_ | Defines the global namespace. | `string` | `application` | **NO** |
| _sampleRate_ | Defines the rate sampling. **Should be a number between [1, 100]**. | `number` | `100` | **NO** |
| _tags_ | Defines the global tags. | `object` | `{}` | **NO** |
| _transport_ | Defines the transport layer to be used to send metrics.<br><br> **Valid Transports:** `udp, http` | `string` | **none** | **YES** |
| _host_ | Defines the host name to where the metrics should be sent. Can also be set inside _api_. | `string` | `127.0.0.1` | **NO** |
| _port_ | Defines the port. Can also be set inside _api_. | `string` | `2013` | **NO** |
| _secure_ | Enable or disable https protocol. Must be set inside _api_. | `boolean` | `true` | **NO** |
| _token_ | Defines the token to be used.  Must be set inside _api_. | `string` | **none** | **NO** |
| _timeout_ | Defines the timeout for the transport layers. Must be set inside _api_. | `number` | `2000` | **NO** |

### Methods

```javascript
- staful.counter('myCounter', 1, {agg: ['sum']});
- staful.gauge('myGauge', 10, { tags: { host: 'localhost' } });
- staful.timer('myCounter', 200, {namespace: 'sandbox'});
```
These methods receive a metric name and a metric value as arguments and send a counter/gauge/timer metric. If the options parameter is omitted, the default values are used.
Read the methods options reference bellow to get more information about the default values.

| Option | Description | Type | Default for Counter | Default for Gauge | Default for Timer |
|:---|:---|:---|:---|:---|:---|
| _agg_ | Defines the aggregations to be executed. These aggregations are merged with the ones configured globally, including method defaults.<br><br> **Valid Aggregations:** `avg, count, sum, first, last, p90, p95, min, max` | `array` | `['avg', 'p90']` | `last]` | `['avg', 'p90', 'count']` |
| _aggFreq_ | Defines the aggregation frequency in **seconds**. It overrides the global aggregation frequency configuration.<br><br> **Valid Aggregation Frequencies:** `10, 30, 60, 20, 180, 300` | `number` | `10` | `10` | `10` |
| _namespace_ | Defines the namespace of the metric. It overrides the global namespace configuration. | `string` | `application` | `application` | `application` |
| _tags_ | Defines the tags of the metric. These tags are merged with the ones configured globally, including method defaults. | `object` | `{}` | `{}` | `{ unit: 'ms' }` |

## Authors

[Mindera - Software Craft](https://github.com/Mindera)

## License

Statful NodeJS Client is available under the MIT license. See the [LICENSE](https://raw.githubusercontent.com/statful/statful-client-objc/master/LICENSE) file for more information.
    
[npm-url]: https://npmjs.org/package/statful-client
[npm-image]: https://badge.fury.io/js/statful-client.svg
