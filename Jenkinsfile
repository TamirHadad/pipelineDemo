node {
    server = rtNewServer(url: "http://localhost:8081/artifactory", username: "admin", password: "password")
    
    // Optional - we can create buildInfo object and pass it to rtDownload and rtUpload and later on publish it.
    def buildInfo = rtCreateBuildInfo()
    
    def downloadArtifacts = {server, currentBuildInfo ->
        return {jsonStr ->
            // Resolve artifacts from artifactory defined as server, by the aql/pattern defined in the json.
            // The resolved artifacts info added to the buildInfo object.
            rtDownload(artifactoryServer: server, buildinfo: currentBuildInfo, json: jsonStr) 
         }
    }
    
    def uploadArtifacts ={server, currentBuildInfo->
        return {jsonStr ->
            // Deploy artifacts to artifactory defined as server, by the aql/pattern defined in the json.
            // The deploied artifacts info added to the buildInfo object.
            rtUpload(artifactoryServer: server,buildinfo: currentBuildInfo, json: jsonStr)
        }
     }
     
    def warmUpJson = readFile 'workspace@script/warmup.json'   
        rtUpload(artifactoryServer: server, json: warmUpJson)
    
    def resolveJson = readFile 'workspace@script/resolve.json' 
    def downloadArtifactsClouser = downloadArtifacts(server, buildInfo);
    downloadArtifactsClouser(resolveJson)
    
    
    def deployJson = readFile 'workspace@script/deploy.json' 
    def uploadArtifactsClouser = uploadArtifacts(server, buildInfo)
    uploadArtifactsClouser(deployJson)
    
    rtPublishBuildInfo(artifactoryServer: server, buildinfo: buildInfo)

   

}
