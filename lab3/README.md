# Microgateway-demo
Learning exercise of Apigee edge microgateway

## Keywords
Apigee public edge, custom modules

## What we learn ?
- How to write custom modules for microgateway


## What is microgateway ?

Apigee Edge Microgateway (MGW) is a lightweight API gateway solution that provides developers with OAuth and API key security, analytics, spike arrest, quota, custom plugin integration, and much more all in a simple service that takes two minutes to set up. It contains 'runtime' of apigee edge that can be run close to the backend or target proximity. Microgateway enables you to use most of the apigee edge features for private traffic which flow only in internal network. Think hybrid. Its main job is to process requests and responses to and from backend services securely while asynchronously pushing valuable API execution data to Apigee Edge where it is consumed by the Edge Analytics system. [more details] (http://docs.apigee.com/microgateway/latest/overview-edge-microgateway)

## What is an Edge Microgateway plugin?

A plugin is a Node.js module that adds functionality to Edge Microgateway. Plugin modules follow a consistent pattern and are stored in a location known to Edge Microgateway, enabling the microgateway to discover and load them automatically.

By default, Edge Microgateway is essentially a secure pass-through proxy that passes requests unchanged to a target. Through plugins, you can add features to the microgateway, such as support for Apigee Edge analytics, OAuth authentication, and rate limiting features like quotas and spike arrest. [more](http://docs.apigee.com/microgateway/v21x/using-plugins-v21x) plugins.

## Pre-requisite
- Finish lab1 or lab2 (configure and install microgateway)

## 1. Writing a custom module

## 2. Copying custom module to plugins directory
### 2.1 Find plugin directory
run the following command, this will give you npm location

``` 
npm config get prefix
```
```
cd {npm_location}/lib/node_modules/edgemicro/plugins
```
Please copy the increment_count(custom_modules/increment_count) directory inside this folder.

## 3. Configuration
### 3.1 Add custom modules to the plugin sequence 
Open ~/.edgemicro/{org}-{env}-config.yaml

Find the following section in the config file
```
...
plugins:
   dir: ../plugins
   sequence:
   - oauth
...
```

Add the custom plugin name to the list. In our case it will be as follow - 
```
...
plugins:
   dir: ../plugins
   sequence:
   - oauth
   - increment_count
...
```


### 3.2 Configure custom plugin
Open ~/.edgemicro/{org}-{env}-config.yaml

Add the following block at the end of the file
```
...
increment_count:
  value: 2 
...
```
Finally the config file would look like
```
...
  plugins:
    sequence:
      - oauth
      - increment_count
headers:
  x-forwarded-for: true
  x-forwarded-host: true
  x-request-id: true
  x-response-time: true
  via: true
oauth:
  allowNoAuthorization: true
  allowInvalidAuthorization: false
  verify_api_key_url: 'https://{organization}-{environemnt}.apigee.net/edgemicro-auth/verifyApiKey'
analytics:
  uri: >-
    https://edgemicroservices-us-east-1.apigee.net/edgemicro/axpublisher/organization/{organization}/environment/{environment}
increment_count:
  value: 2
...
```

## 4. Re-start or Reload the microgateway
Press **CTRL C** to stop the microgateway and run the start command as follow -  
```
edgemicro start -o {organization} -e {environment} -k {key} -s {secret}
```
or

Run the following command - 
```
edgemicro reload -o {organization} -e {environment} -k {key} -s {secret}
```

## 5. Make API calls
```
curl -X POST -H 'Content-type: application/json' -d '{"count": 2}' http://localhost:8000/{proxy-name}/post
```

You should see that the count in response sould have incremented by the value you specifed in config file.
```
{
  "args": {},
  "data": "{\"count\":4}",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Content-Length": "11",
    "Content-Type": "application/json",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.51.0",
    "Via": "1.1 localhost",
    "X-Forwarded-Host": "localhost:8000",
    "X-Request-Id": "86ddcdc0-0a1a-11e7-a0b9-d947906ca989.895c8960-0a1a-11e7-a0b9-d947906ca989"
  },
  "json": {
    "count": 4
  },
  "origin": "::1, 111.93.155.244",
  "url": "http://localhost:8000/post"
}
```

