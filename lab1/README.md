# Microgateway-demo
Learning exercise of Apigee edge microgateway

## Keywords
Apigee public edge, microgateway, oauth, spikearrest, analytics

## What we learn ?
- How to use microgateway with apigee public edge
- PART - 1 : How to setup microgateway for a simple proxy
- PART - 2 : How to use oauth for api calls in microgateway
- PART - 3 : How to use existing plugins for microgateway
- PART - 4 : Analytics

**Advanced learning** : How to create custom plugins ?

## What is microgateway ?

Apigee Edge Microgateway (MGW) is a lightweight API gateway solution that provides developers with OAuth and API key security, analytics, spike arrest, quota, custom plugin integration, and much more all in a simple service that takes two minutes to set up. It contains 'runtime' of apigee edge that can be run close to the backend or target proximity. Microgateway enables you to use most of the apigee edge features for private traffic which flow only in internal network. Think hybrid. Its main job is to process requests and responses to and from backend services securely while asynchronously pushing valuable API execution data to Apigee Edge where it is consumed by the Edge Analytics system. [more details] (http://docs.apigee.com/microgateway/latest/overview-edge-microgateway)

## Pre-requisite
- nodejs > v4.2.0
- microgateway v2.3.x

you also need access to an Apigee edge paid org. Edge Microgateway does not work with Free Trial organizations. If you have a Trial org and want to use Edge Microgateway, contact Apigee sales for more information. 

# PART - 1

## 1. Installation
- refer [this](https://nodejs.org/en/download/) to install nodejs.
- refer [this](http://docs.apigee.com/microgateway/latest/installing-edge-microgateway) to install microgatway.

## 2. Configuration
### 2.1 Configure microgateway
run the following command

``` 
edgemicro configure -o {organization} -e {environment} -u {Apigee account username}
```

The output will include important information about your configuration, it will display the location of config file where it saves the configuration. **eg** : ~/.edgemicro/{org}-{env}-config.yaml

It’ll also display your configuration’s credentials required for start up:

key: 0c4449144dc894f68913a7385dbae92f64df915ce2e76a2ff45cbdb5fb3581

secret: 75a10ceb40bd31068ae4a334198566e1f0a4f3f84536100e935b9e85fbfa

Make note of these credentials

### 2.2 Disabling oauth

For the demo purpose we shall disable oauth using config file that got generated during configuration phase.

Open ~/.edgemicro/{org}-{env}-config.yaml
Find the following section in the config file
```
...
oauth:
  allowNoAuthorization: false
  allowInvalidAuthorization: false
...
```
Change **allowNoAuthorization** to true. Finally it should look like follow
```
...
oauth:
  allowNoAuthorization: true
  allowInvalidAuthorization: false
...
```

## 3. Creating proxy
We have created a simple proxy which targets to http://httpbin.org.

Let's use this proxy for the demo.

Please zip the **apiproxy** folder found in this folder.

Create a proxy from the edge ui https://enterprise.apigee.com/platform/{org-name}/apis
- Edge Microgateway-aware proxy names must always begin with the prefix edgemicro_. For example: edgemicro_hello 
- make sure you select import from proxy bundle while creating the proxy.

## 4. Run microgateway
Run the following command to start edge microgateway
```
edgemicro start -o {organization} -e {environment} -k {key} -s {secret}
```

{key} and {secret} is obtained from the output of configuration command.

## 5. Test
Make any api call to the proxy
```
curl http://localhost:8000/{proxy-name}/?name=gautham
```
output:
```
{
  "args": {
    "name": "gautham"
  },
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.51.0",
    "Via": "1.1 localhost",
    "X-Authorization-Claims": "eyJzY29wZXMiOltdfQ==",
    "X-Forwarded-Host": "localhost:8000",
    "X-Request-Id": "9a122a60-0968-11e7-931c-799bda579a61.d7a9b950-0969-11e7-931c-799bda579a61"
  },
  "origin": "::1, 111.93.155.244",
  "url": "http://localhost:8000/get?name=gautham"
}
```

## 6. Stop microgateway
```
Ctrl C
```


# PART - 2

In this section we shall secure our api with oauth.

## 1. Configuration
### 1.1 Enabling oauth

We shall enable oauth using the same config file.

Open ~/.edgemicro/{org}-{env}-config.yaml

Find the following section in the config file
```
...
oauth:
  allowNoAuthorization: true
  allowInvalidAuthorization: false
...
```
Change **allowNoAuthorization** to flase. Finally it should look like follow
```
...
oauth:
  allowNoAuthorization: false
  allowInvalidAuthorization: false
...
```
### 1.2 Add oauth plugin

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

If you dont find oauth in the list of plugins, please add it.

## 2. Creating product, developer, app
### 2.1 Create product
Create a product https://enterprise.apigee.com/platform/{org-name}/products

Make sure you select access type as public and add the newly created proxy in this product.

### 2.2 Create developer
Create a developer https://enterprise.apigee.com/platform/{org-name}/developers

### 2.3 Create app
Create a app https://enterprise.apigee.com/platform/{org-name}/app

Make sure you add the product just created.

## 3. Re-start or Reload the microgateway
Press **CTRL C** to stop the microgateway and run the start command as follow -  
```
edgemicro start -o {organization} -e {environment} -k {key} -s {secret}
```
or

Run the following command - 
```
edgemicro reload -o {organization} -e {environment} -k {key} -s {secret}
```

## 4. Make failure API calls
```
curl http://localhost:8000/{proxy-name}/?name=gautham
```
This call shall fail will 
```
{"error":"missing_authorization","error_description":"Missing Authorization header"}
```

You need to pass valid credentials from the app you created to make the call.

## 5. Generate token
Run the following command to generate access token
```
edgemicro token get -o {organization} -e {environment} -i {consumer_key} -s {consumer_secret}
```
You can collect {consumer_key} and {consumer_secret} from the app you created.

you will get a token as follow
```
{ token: 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhcHBsaWNhdGlvbl9uYW1lIjoiY2VkMjk5NjgtNDdhYi00YjVlLWI2ZTUtNGVhYTlkM2FkYzZhIiwiY2xpZW50X2lkIjoiNTZOSkhQSFNGZHkwaHhYck1LNUNsYUo1VkFvckpaUkUiLCJzY29wZXMiOltdLCJhcGlfcHJvZHVjdF9saXN0IjpbIm1pY3JvZ2F0ZXdheS1kZW1vIl0sImlhdCI6MTQ4OTU3NDAyOSwiZXhwIjoxNDg5NTc1ODI4fQ.FMhA8kW7mztNRuvL_GdJfI76EkV3QLB1Xvxd5S9uAzbBk_0LAWEuYulDIa-3or_0K9X9T0u8J98nGcqUrYgVwKNQu4vupSSpcIEl4Mw_guuAddsJTorl7Pno7tPyz485qEIM_E3c6iBQv02R0pShc7wKUj-SdPsQcEpBQcUA8pe7MRGC3f7vXWrOJpc0_4Rj70LWeo7cl5r_PyW0z2jCoTHHEr2wwl4-2cC6KFGx7QPQJb8SXCEINMT4sOgo7z3PtSjFgHP2-EvsnswhGsD_eKfZ5-UYsHr4fD3Ey_6yb83VSsWX7aZcE2S15QYNpqmFZ3K6qUCUssKjRlyTzRIK_g' }
```

## 4. Make successfull API calls
```
curl -H 'Authorization: Bearer {token}' http://localhost:8000/{proxy-name}/?name=gautham
```
This call shall pass with
```
{
  "args": {
    "name": "gautham"
  },
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.51.0",
    "Via": "1.1 localhost",
    "X-Authorization-Claims": "eyJzY29wZXMiOltdfQ==",
    "X-Forwarded-Host": "localhost:8000",
    "X-Request-Id": "9a122a60-0968-11e7-931c-799bda579a61.d7a9b950-0969-11e7-931c-799bda579a61"
  },
  "origin": "::1, 111.93.155.244",
  "url": "http://localhost:8000/get?name=gautham"
}
```

You need to pass valid credentials from the app you created to make the call.


# PART - 3

In this section we shall see how to use existing plugins for microgateway. One such existing plugin is - **spikearrest**, which we are using in this demo.

more pugins can be found [here](http://docs.apigee.com/microgateway/latest/use-plugins)

## 1. Configure
### 1.2 Disabling oauth

For the demo purpose we shall disable oauth

Open ~/.edgemicro/{org}-{env}-config.yaml
Find the following section in the config file
```
...
oauth:
  allowNoAuthorization: false
  allowInvalidAuthorization: false
...
```
Change **allowNoAuthorization** to true. Finally it should look like follow
```
oauth:
  allowNoAuthorization: true
  allowInvalidAuthorization: false
```

### 1.2 Add spikearrest plugin

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

Add spikearrest to the plugin list, before oauth (ordering is important)

The config file would look like
```
...
plugins:
   dir: ../plugins
   sequence:
   - spikearrest
   - oauth
...
```

### 1.3 Configure spikearrest plugin

Open ~/.edgemicro/{org}-{env}-config.yaml

Add the following block at the end of the file
```
...
spikearrest:
  timeUnit: minute   
  allow: 5   
  buffersize: 0 
...
```
Finally the config file would look like
```
...
  plugins:
    sequence:
      - oauth
      - spikearrest
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
spikearrest:
  timeUnit: minute
  allow: 5
  buffersize: 0
...
```

## 2. Re-start or Reload the microgateway
Press **CTRL C** to stop the microgateway and run the start command as follow -  
```
edgemicro start -o {organization} -e {environment} -k {key} -s {secret}
```
or

Run the following command - 
```
edgemicro reload -o {organization} -e {environment} -k {key} -s {secret}
```


## 3. Make API calls
```
curl http://localhost:8000/{proxy-name}/?name=gautham
```

Try hitting the api with frequency > 10TPS

You should see error message as follow when the limit is reached
```
{"message":"SpikeArrest engaged","status":503}
```

# PART - 4

### 1. Analytics
Login to the edge UI

Navigate to Analytics > Proxy Performance

Select your proxy

The graph shows you information about the the proxy's traffic patterns, such as total traffic, average response time, average target response time, and more.

# Advanced

This topic is intended for developers who wish extend Edge Microgateway features by writing custom plugins. If you wish to write a new plugin, experience with JavaScript and Node.js is required. [click me](https://github.com/kidiyoor/microgateway-demo/tree/master/lab3)
