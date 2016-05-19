#Artifactory pipeline plugin DSL

The download, upload and publish build info are all actions related to the Artifactory server.<br>
For download and upload steps please read about the Artifacory Json DSL [scheme](#schema).<br>
In order to perform the actions above we first need to have a valid artifactory server.

###So how can I get an Server?
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

# Artifacory JSON DSL
## Schema
### Download/Upload

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


