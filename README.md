# Microgateway-demo
Learning exercise of Apigee edge microgateway

## keywords
Apigee public edge, docker

## what we learn ?
- How to use microgateway with apigee public edge
- How to run microgateway in docker 


## What is microgateway ?

Apigee Edge Microgateway (MGW) is a lightweight API gateway solution that provides developers with OAuth and API key security, analytics, spike arrest, quota, custom plugin integration, and much more all in a simple service that takes two minutes to set up. It contains 'runtime' of apigee edge that can be run close to the backend or target proximity. Microgateway enables you to use most of the apigee edge features for private traffic which flow only in internal network. Think hybrid. Its main job is to process requests and responses to and from backend services securely while asynchronously pushing valuable API execution data to Apigee Edge where it is consumed by the Edge Analytics system. [more details] (http://docs.apigee.com/microgateway/latest/overview-edge-microgateway)

## Pre-requisite
- docker
- nodejs > v4.2.0
- microgateway v2.3.x

you also need access to an Apigee edge paid org. Edge Microgateway does not work with Free Trial organizations. If you have a Trial org and want to use Edge Microgateway, contact Apigee sales for more information. 

## Installation
### Step - 1
#### Install Docker Nodejs

- refer [this](https://docs.docker.com/engine/installation/) to install docker.
- refer [this](https://nodejs.org/en/download/) to install nodejs.
- refer [this](http://docs.apigee.com/microgateway/latest/installing-edge-microgateway) to install microgatway.

### Step - 2
#### Configure microgateway
run the folling command

```edgemicro configure -o <organization> -e <environment> -u <Apigee account username>
```

### Step - 3

