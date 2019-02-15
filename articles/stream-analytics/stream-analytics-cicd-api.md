---
title: 'Implementera CI/CD för Azure Stream Analytics på IoT Edge med hjälp av REST API: er'
description: 'Lär dig hur du implementerar en kontinuerlig integrering och distributionspipeline för Azure Stream Analytics med hjälp av REST API: er.'
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 40beb620e037061b189762a51e3c29d0fd251b27
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268507"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementera CI/CD för Stream Analytics på IoT Edge med API: er

Du kan aktivera kontinuerlig integrering och distribution för Azure Stream Analytics-jobb med hjälp av REST API: er. Den här artikeln innehåller exempel på vilka API: er ska användas och hur de används. REST API: er stöds inte på Azure Cloud Shell.

## <a name="call-apis-from-different-environments"></a>Anropa API: er från olika miljöer

REST API: er kan anropas från både Linux och Windows. Följande kommandon visar korrekt syntax för API-anrop. Specifika API-användning kan beskrivas i senare avsnitt av den här artikeln.

### <a name="linux"></a>Linux

Du kan använda för Linux, `Curl` eller `Wget` kommandon:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

För Windows, använder du Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Skapa ett ASA-jobb i Microsoft Edge 
 
Skapa Stream Analytics-jobb genom att anropa PUT-metoden med hjälp av Stream Analytics-API.

|Metod|URL för begäran|
|------|-----------|
|PLACERA|https://management.azure.com/subscriptions/{**prenumerations-id**} /resourcegroups/ {**Resursgruppsnamn-**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobbnamn**}? api-version = 2017-04-01-preview|
 
Exempel på kommando med hjälp av **curl**:

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Exempel på begärandetexten i JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Mer information finns i den [API-dokumentation](/rest/api/streamanalytics/stream-analytics-job).  
 
## <a name="publish-edge-package"></a>Publicera Edge-paketet 
 
Anropa metoden POST med hjälp av Edge-paketet publicera API: et för att publicera ett Stream Analytics-jobb på IoT Edge.

|Metod|URL för begäran|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**} /resourceGroups/ {**resourcegroupname**} / providers/Microsoft.StreamAnalytics/streamingjobs/ {**jobname**} / publishedgepackage? api-version = 2017-04-01 - förhandsversion|

Den här asynkrona åtgärden returnerar statusvärdet 202 tills jobbet har publicerats. Plats-svarshuvudet innehåller den URI som används för att hämta status för processen. När processen körs, returnerar ett anrop till URI: N i location-huvudet 202 status. När processen är klar Returnerar URI: N i location-huvudet statusen 200. 

Exempel på en Edge-paketet publicera skriptanrop med **curl**: 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
När du har gjort anropet POST, bör du förväntar dig ett svar med en brödtext. Leta efter URL: en finns i huvud i svaret och spara den för framtida användning.
 
Exempel på URL: en från början av svar:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Väntan på en till två minuter innan du kör följande kommando för att göra ett API-anrop med URL-Adressen som du hittat i huvud i svaret. Försök igen om du inte får ett 200-svar.
 
Exempel på att göra API-anrop med returneras URL: en med **curl**:

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Svaret innehåller den information du behöver för att lägga till skriptet för Edge-distribution. Exemplen nedan visar information som du behöver samla in och var du vill lägga till den i distributionen manifest.
 
Exempel-svarstext när du har publicerat har:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

Exempel på distribution Manifest: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Efter konfigurationen av manifestet distribution avser [distribuera Azure IoT Edge-moduler med Azure CLI](../iot-edge/how-to-deploy-modules-cli.md) för distribution.


## <a name="next-steps"></a>Nästa steg 
 
* [Azure Stream Analytics på IoT Edge](stream-analytics-edge.md)
* [ASA på IoT Edge-självstudie](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Utveckla Stream Analytics Edge-jobb med hjälp av Visual Studio-verktyg](stream-analytics-tools-for-visual-studio-edge-jobs.md)
