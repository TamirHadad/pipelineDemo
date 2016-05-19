# Artifactory Jenkins Plugin Snapshot - Pipeline Support

## Introduction
In contrast to freestyle jobs, pipelines enable you to define the whole application lifecycle. <br>
A pipeline is a Groovy script that tells Jenkins what to do when your Pipeline is run.<br>
For more details on Pipeline, check out the [Pipeline tutorial](https://github.com/jenkinsci/pipeline-plugin/blob/master/TUTORIAL.md) on GitHub or the [Getting started with Pipeline](https://jenkins.io/doc/pipeline/) documentation.<br>
The [Artifactory Jenkins Plugin](https://github.com/JFrogDev/jenkins-artifactory-plugin) now provides a limited support to Pipelines. This means that you can now perform Artifactory-related actions like downloading and uploading artifacts at any stage of your Pipeline flow and have a better and more flexible CD workflow.<br>
This support will increasingly improve and allow more Artifactory actions from Pipelines and a better integration between Jenkins and the JFrog platform.<br>
Please notice that this is a snapshot version for selected customers and not an official one.

## Overview
This plugin adds a new global variable called Artifactory to Pipeline scripts.<br>
The Artifactory variable can be used to define Server object/s.<br>
Each server object represents a single Artifactory server instance and can be used to perform actions against this server instance.<br>
The actions that are currently available are: download, upload and publishBuildInfo.

## Installing the plugin
Note: This is a snapshot version. But stay tuned, we plan to release a beta version soon.<br><br> 

Download (link) <br><br>

Before you begin, ensure Pipeline is installed: navigate to Plugin Manager, install Pipeline and restart Jenkins.<br>
If you plan to use the plugin using the Pipeline Git integration, you need to ensure you have the Git plugin installed as well.<br><br>

To install the plugin, please do the following:
* Download the plugin .hpi file from the link above
* Go to ‘Manage Jenkins’ → ‘Manage Plugins’ → ‘Advanced’ tab
* In the ‘Upload Plugin’ section, select the path to the .hpi file and click ‘Upload’
* Restart Jenkins

## Running a Code Sample
There are two ways to use the Artifactory integration from a Pipeline flow:
* Using the Pipeline Git integration
* Using a regular Pipeline script

### Using the Pipeline Git integration
* Go to ‘Manage Jenkins’ → ‘Configure System’ → ‘Artifactory’ section, and make sure you have your Artifactory server/s configured
* Go to ‘Manage Jenkins’ → ‘Configure System’ → ‘Artifactory’ section, and make sure you have your Artifactory server/s configured
* Be sure to set the ‘server id’ for the Artifactory server/s, we’ll use that later from the Pipeline to identify the server/s we work with
* Create a new Pipeline item
* In the ‘Pipeline’ section, in the ‘Definition’ field, select the ‘Pipeline script from SCM’ option, and then in the ‘SCM’ field, select the ‘Git’ option
* Copy the link to this GitHub repository and paste it to the ‘Repository URL’ field
* In the ‘Script Path’ field, put the path to the script file of the example you wish to run in the following format: <EXAMPLE-NAME>/Jenkinsfile. For example: setting the value to  ‘props-example/Jenkinsfile’ would run the script in the props-example folder and use the json files in the same folder (see Artifactory DSL chapter for more details)
* Add a parameter by checking the 'This build is parameterized' option, and then adding a text parameter called 'SERVER_ID' with the value of the server id you configured in the global Artifactory configuration  
* Save and build the Pipeline flow

### Using a regular Pipeline script
* Go to ‘Manage Jenkins’ → ‘Configure System’ → ‘Artifactory’ section, and make sure you have your Artifactory server/s configured
* Be sure to set the ‘server id’ for the Artifactory server/s, we’ll use that later from the Pipeline to identify the server/s we work with
* Create a new Pipeline item
* Copy the contents of the Jenkinsfile from the folder of the example you want to run into the ‘Script’ field
* Add a parameter by checking the 'This build is parameterized' option, and then adding a text parameter called 'SERVER_ID' with the value of the server id you configured in the global Artifactory configuration  
* Save and build the Pipeline flow

## Artifactory pipeline plugin DSL

The download, upload and publish build info are all actions related to the Artifactory server.<br>
For download and upload steps please read about the Artifacory Json DSL [scheme](#schema).<br>
In order to perform the actions above we first need to have a valid artifactory server.

### So how can I get an Server?
you have two options:
  1. Artifactory.server('serverName')
  2. Artifactory.newServer('serverUrl', 'user', 'password')
The return value of those action is an Artifactory server.
def server = Artifactory.server('serverName')

### I have a server, what is next?

#### Download artifacts

```
def artifactoryDownloadDsl = '{
 "files": [
  {
      "pattern": "libs-snapshot-local/*.zip",
      "target": "Bazinga/"
    }
 ]
}'

server.download(artifactoryDownloadDsl)
```

#### Upload artifacts

```
def artifactoryUploadDsl = '{
  "files": [
    {
      "pattern": "files/*Artifactory*.zip",
      "target": "libs-snapshot-local"
    }
 ]
}'
server.upload(artifactoryDownloadDsl)
```

###### Both, the download and the upload steps retrun a build info object.

#### Publish build info

```
def buildInfo1 = server.download(artifactoryDownloadDsl)
def buildInfo2 = server.upload(artifactoryDownloadDsl)
buildInfo1.append(buildInfo2)
server.publishBuildInfo(buildInfo1)
```
##### OR


```
def buildInfo = Artifactory.newBuildInfo()
server.download(artifactoryDownloadDsl, buildInfo)
server.upload(artifactoryDownloadDsl, buildInfo)
server.publishBuildInfo(buildInfo1)
```

## Artifacory JSON DSL
For the [Jfrog-cli](https://www.jfrog.com/confluence/display/RTF/JFrog+CLI) users the DSL might look familiar.

### Schema
#### Download/Upload

```
{
  "files": [
    {
      "pattern": "[Mandatory]",
      "target": "[Mandatory]",
      "recursive": "[Optional]",
      "flat" : "[Optional]",
      "props": "[Optional]"
    }
  ]
}
```

##### For download you can also use [aql](https://www.jfrog.com/confluence/display/RTF/Artifactory+Query+Language) instead of pattern

```
{
  "files": [
    {
      "aql": "[Mandatory]",
      "target": "[Mandatory]",
      "recursive": "[Optional]",
      "flat" : "[Optional]",
      "props": "[Optional]"
    }
  ]
}
```
