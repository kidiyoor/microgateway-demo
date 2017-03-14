# Microgateway-demo
Learning exercise of Apigee edge microgateway

## Keywords
Apigee public edge, docker

## What we learn ?
- How to use microgateway with apigee public edge
- How to run microgateway in docker 


## What is microgateway ?

Apigee Edge Microgateway (MGW) is a lightweight API gateway solution that provides developers with OAuth and API key security, analytics, spike arrest, quota, custom plugin integration, and much more all in a simple service that takes two minutes to set up. It contains 'runtime' of apigee edge that can be run close to the backend or target proximity. Microgateway enables you to use most of the apigee edge features for private traffic which flow only in internal network. Think hybrid. Its main job is to process requests and responses to and from backend services securely while asynchronously pushing valuable API execution data to Apigee Edge where it is consumed by the Edge Analytics system. [more details] (http://docs.apigee.com/microgateway/latest/overview-edge-microgateway)

## Pre-requisite
- docker
- nodejs > v4.2.0
- microgateway v2.3.x

you also need access to an Apigee edge paid org. Edge Microgateway does not work with Free Trial organizations. If you have a Trial org and want to use Edge Microgateway, contact Apigee sales for more information. 

## 1. Installation
### 1.1
#### Install Docker Nodejs

- refer [this](https://docs.docker.com/engine/installation/) to install docker.
- refer [this](https://nodejs.org/en/download/) to install nodejs.
- refer [this](http://docs.apigee.com/microgateway/latest/installing-edge-microgateway) to install microgatway.

## 2. Configuration
### 2.1
#### Configure microgateway
run the following command

``` 
edgemicro configure -o {organization} -e {environment} -u {Apigee account username}
```

The output will include important information about your configuration, it will display the location of config file where it saves the configuration. **eg** : ~/.edgemicro/{org}-{env}-config.yaml

It’ll also display your configuration’s credentials required for start up:

key: 0c4449144dc894f68913a7385dbae92f64df915ce2e76a2ff45cbdb5fb3581

secret: 75a10ceb40bd31068ae4a334198566e1f0a4f3f84536100e935b9e85fbfa

### 2.2
#### creating env file
save these credentials to a file. **eg**: ~/.edgemicro/{org}-{env}-env.list

```
EDGEMICRO_KEY=0c4449144dc894f68913a7385dbae92f64df9157ce2e76a2ff45cbd
EDGEMICRO_SECRET=75a10ceb40bd31068ae54a334198566e1f0a4f3fd845361a
EDGEMICRO_ORG=<org>
EDGEMICRO_ENV=<env>
```

these will be set as environment variables for the edgemicro during boot up.

### 2.3
#### Disabling oauth

For the demo purpose we shall disable oauth using config file that got generated during configuration phase.

Open ~/.edgemicro/{org}-{env}-config.yaml
Find the following section in the config file
```
oauth:
  allowNoAuthorization: true
  allowInvalidAuthorization: false
```
Change **allowNoAuthorization** to flase. Finally it should look like follow
```
oauth:
  allowNoAuthorization: false
  allowInvalidAuthorization: false
```


## 3. Run microgateway
### 3.1
#### pull docker image
```
docker pull ndietz/emgw
```

### 3.2
#### start
run the following command to start edge microgateway
```
docker run --env-file ./<org>-<env>-env.list  -p 8000:8000 -v {directory containing configuration}:/root/.edgemicro -d -t ndietz/emgw
```

{directory containing configuration} will be usually ~/.edgemicro

## 4. Test
### 4.1
Check if the container is running
```
docker ps
```

### 4.2
Make any api call to the proxy
```
curl -i localhost:8000/myapi
```

## 5. Stop microgateway
### 5.1
Get the container id
```
docker ps
```

### 5.2
Run the kill command
```
docker kill {container_id}
```
