---
title: Använd REST för att hantera ML-resurser
titleSuffix: Azure Machine Learning
description: Så här använder du REST-API:er för att skapa, köra och ta bort Azure ML-resurser
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 419dbd998abc5cbd2da64a990e13d46f3fb2efbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77580636"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>Skapa, köra och ta bort Azure ML-resurser med REST

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Det finns flera sätt att hantera dina Azure ML-resurser. Du kan använda [portalen,](https://portal.azure.com/) [kommandoradsgränssnittet](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)eller [Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py). Eller så kan du välja REST API. REST API använder HTTP-verb på ett standardiserat sätt för att skapa, hämta, uppdatera och ta bort resurser. REST API fungerar med alla språk eller verktyg som kan göra HTTP-begäranden. REST:s enkla struktur gör det ofta till ett bra val i skriptmiljöer och för MLOps-automatisering. 

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hämta en auktoriseringstoken
> * Skapa en korrekt formaterad REST-begäran med hjälp av autentisering av tjänstens huvudnamn
> * Använda GET-begäranden för att hämta information om Azure ML:s hierarkiska resurser
> * Använda PUT- och POST-begäranden för att skapa och ändra resurser
> * Använda DELETE-begäranden för att rensa resurser 
> * Använda nyckelbaserad auktorisering för att få inbetalade modeller

## <a name="prerequisites"></a>Krav

- En **Azure-prenumeration** som du har administrativa rättigheter för. Om du inte har en sådan prenumeration kan du prova den [kostnadsfria eller betalda personliga prenumerationen](https://aka.ms/AMLFree)
- En [Azure Machine Learning-arbetsyta](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- Administrativa REST-begäranden använder tjänstens huvudautentisering. Följ stegen i [Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) för att skapa ett huvudnamn för tjänsten på arbetsytan
- **Curl verktyget.** **Curl-programmet** finns i [Windows-delsystemet för Linux](https://aka.ms/wslinstall/) eller någon UNIX-distribution. I PowerShell är **curl** ett alias för **Invoke-WebRequest** och `curl -d "key=val" -X POST uri` blir `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`. 

## <a name="retrieve-a-service-principal-authentication-token"></a>Hämta en autentiseringstoken för tjänstens huvudnamn

Administrativa REST-begäranden autentiseras med ett implicit flöde enligt OAuth2. Det här autentiseringsflödet använder en token som tillhandahålls av prenumerationens tjänsthuvudnamn. För att hämta den här token behöver du:

- Ditt klient-ID (identifiera den organisation som din prenumeration tillhör)
- Ditt klient-ID (som kommer att associeras med den skapade token)
- Din klient hemlighet (som du bör skydda)

Du bör ha dessa värden från svaret på skapandet av tjänstens huvudnamn som beskrivs i [Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication). Om du använder företagsprenumerationen kanske du inte har behörighet att skapa ett huvudnamn för tjänsten. I så fall bör du använda antingen en [gratis eller betald personlig prenumeration](https://aka.ms/AMLFree).

Så här hämtar du en token:

1. Öppna ett terminalfönster
1. Ange följande kod på kommandoraden
1. Ersätt dina egna `{your-tenant-id}` `{your-client-id}`värden `{your-client-secret}`med , och . I hela den här artikeln är strängar omgivna av lockiga parenteser variabler som du måste ersätta med dina egna lämpliga värden.
1. Köra kommandot

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

Svaret ska ge en åtkomsttoken som är bra i en timme:

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

Anteckna token, eftersom du använder den för att autentisera alla efterföljande administrativa begäranden. Du gör det genom att ange ett auktoriseringshuvud i alla begäranden:

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

Observera att värdet börjar med strängen "Bärare" inklusive ett enda blanksteg innan du lägger till token.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>Hämta en lista över resursgrupper som är associerade med din prenumeration

Om du vill hämta listan över resursgrupper som är associerade med prenumerationen kör du:

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

I Azure publiceras många REST-API:er. Varje tjänsteleverantör uppdaterar sitt API på egen hand, men gör det utan att bryta befintliga program. Tjänsteleverantören använder `api-version` argumentet för att säkerställa kompatibilitet. Argumentet `api-version` varierar från service till service. För Machine Learning Service, till exempel, `2019-11-01`är den aktuella API-versionen . För lagringskonton är `2019-06-01`det . För nyckelvalv är `2019-09-01`det. Alla REST-anrop `api-version` bör ställa in argumentet till det förväntade värdet. Du kan lita på syntaxen och semantiken för den angivna versionen även när API:et fortsätter att utvecklas. Om du skickar en begäran `api-version` till en provider utan argumentet innehåller svaret en människoläsbar lista med värden som stöds. 

Ovanstående samtal kommer att resultera i en komprimerad JSON svar av formuläret: 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>Öka detaljnivån i arbetsytor och deras resurser

Om du vill hämta uppsättningen arbetsytor i en resursgrupp `{your-resource-group}`kör `{your-access-token}`du följande, ersätter `{your-subscription-id}`, och: 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Återigen får du en JSON-lista, den här gången som innehåller en lista, var och en objekt som innehåller en arbetsyta:

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

Om du vill arbeta med resurser inom en arbetsyta växlar du från den allmänna **management.azure.com-servern** till en REST API-server som är specifik för arbetsytans plats. Notera värdet på `discoveryUrl` nyckeln i ovanstående JSON-svar. Om du får den webbadressen får du ett svar i stil med:

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

Värdet för `api` svaret är URL:en för den server som du ska använda för ytterligare begäranden. Om du till exempel vill visa en lista över experiment skickar du följande kommando. Ersätt `regional-api-server` med svarets `api` värde (till `centralus.api.azureml.ms`exempel ). Också `your-subscription-id`ersätta `your-resource-group` `your-workspace-name`, `your-access-token` , och som vanligt:

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Om du vill hämta registrerade modeller på arbetsytan skickar du:

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Observera att för att lista experiment `history/v1.0` sökvägen börjar med medan `modelmanagement/v1.0`att lista modeller, börjar sökvägen med . REST API är indelat i flera operativa grupper, var och en med en distinkt sökväg. API-referensdokumenten på länkarna nedan listar åtgärder, parametrar och svarskoder för de olika åtgärderna.

|Område|Sökväg|Referens|
|-|-|-|
|Artefakter|artefakt/v2.0/|[Referens för REST-API](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|Datalager|datastore/v1.0/|[Referens för REST-API](https://docs.microsoft.com/rest/api/azureml/datastores)|
|Finjustering av hyperparametrar|hyperdrift/v1.0/|[Referens för REST-API](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|Modeller|modellmanagement/v1.0/|[Referens för REST-API](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|Körningshistorik|utförande/v1.0/ och historia/v1.0/|[Referens för REST-API](https://docs.microsoft.com/rest/api/azureml/runs)|

Du kan utforska REST API med hjälp av det allmänna mönstret för:

|URL-komponent|Exempel|
|-|-|
| https://| |
| regional-api-server/ | centralus.api.azureml.ms/ |
| operationer-sökväg/ | historia/v1.0/ |
| prenumerationer/{din prenumeration-id}/ | abonnemang/abcde123-abab-abab-1234-0123456789abc/ |
| resourceGroups/{your-resource-group}/ | resourceGroups/MyResourceGroup/ |
| leverantörer/driftleverantör/ | leverantörer/Microsoft.MachineLearningServices/ |
| provider-resurs-sökväg/ | arbetsytor/MLWorkspace/MyWorkspace/FirstExperiment/runs/1/ |
| drift-slutpunkt/ | artefakter/metadata/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>Skapa och ändra resurser med put- och post-begäranden

Förutom resurshämtning med GET-verbet stöder REST API att skapa alla resurser som krävs för att träna, distribuera och övervaka ML-lösningar. 

Utbildning och körning av ML-modeller kräver beräkningsresurser. Du kan lista beräkningsresurserna för en arbetsyta med: 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

Om du vill skapa eller skriva över en namngiven beräkningsresurs använder du en PUT-begäran. I det följande, förutom de nu välbekanta `your-resource-group` `your-workspace-name`ersättningarna `your-access-token`av `your-compute-name` `your-subscription-id`, , `location` `vmSize`, och , ersättning , och värden för , , `vmPriority`, `scaleSettings`, , `adminUserName`och `adminUserPassword`. Som anges i referensen vid [Machine Learning Compute - Create Or Update SDK Reference](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate), skapar följande kommando en dedikerad, enkelnod Standard_D1 (en grundläggande CPU-beräkningsresurs) som skalas ned efter 30 minuter:

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> I Windows-terminaler kan du behöva fly från dubbelcitatsymbolerna när du skickar JSON-data. Det vill än, `"location"` `\"location\"`text som blir . 

En lyckad begäran `201 Created` kommer att få ett svar, men observera att detta svar helt enkelt innebär att etableringsprocessen har börjat. Du måste avsöka (eller använda portalen) för att bekräfta att den har slutförts.

### <a name="create-an-experimental-run"></a>Skapa en experimentell körning

Om du vill starta en körning i ett experiment behöver du en zip-mapp som innehåller träningsskriptet och relaterade filer och en JSON-fil med körningsdefinition. Zip-mappen måste ha Python-postfilen i sin rotkatalog. Som ett exempel, zip ett trivialt Python-program som följande i en mapp som heter **train.zip**.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

Spara nästa utdrag som **definition.json**. Bekräfta att värdet "Script" matchar namnet på Python-filen som du just zippade upp. Bekräfta värdet "Target" matchar namnet på en tillgänglig beräkningsresurs. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

Skicka dessa filer till `multipart/form-data` servern med innehåll:

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

En lyckad POST-begäran genererar en `200 OK` status med en svarstext som innehåller identifieraren för den skapade körningen:

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

Du kan övervaka en körning med rest-ful mönster som nu bör vara bekant:

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>Ta bort resurser som du inte längre behöver

Vissa, men inte alla, resurser stöder DELETE-verbet. Kontrollera [API-referensen](https://docs.microsoft.com/rest/api/azureml/) innan du bestämmer dig för att använda REST API för borttagning av användningsfall. Om du till exempel vill ta bort en modell kan du använda:

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>Använd REST för att göra mål på en distribuerad modell

Det är möjligt att distribuera en modell så att den autentiseras med ett huvudnamn för tjänsten, men de flesta klientinriktade distributioner använder nyckelbaserad autentisering. Du hittar rätt nyckel på distributionens sida på fliken **Slutpunkter** i Studio. Samma plats visar slutpunktens poäng-URI. Modellens indata måste modelleras som en `data`JSON-matris med namnet:

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>Skapa en arbetsyta med REST 

Varje Azure ML-arbetsyta har ett beroende av fyra andra Azure-resurser: ett behållarregister med administration aktiverat, ett nyckelvalv, en Application Insights-resurs och ett lagringskonto. Du kan inte skapa en arbetsyta förrän dessa resurser finns. Mer information om hur du skapar varje sådan resurs finns i REST API-referensen.

Om du vill skapa en arbetsyta placerar `management.azure.com`du ett anrop som liknar följande . Även om det här anropet kräver att du anger ett stort antal variabler, är det strukturellt identiskt med andra anrop som den här artikeln har diskuterat. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

Du bör `202 Accepted` få ett svar och, i `Location` de returnerade rubrikerna, en URI. Du kan hämta den här URI:n för information om distributionen, inklusive användbar felsökningsinformation om det finns ett problem med en av dina beroende resurser (till exempel om du har glömt att aktivera administratörsåtkomst i behållarregistret). 

## <a name="troubleshooting"></a>Felsökning

### <a name="resource-provider-errors"></a>Resursproviderfel

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Flytta arbetsytan

> [!WARNING]
> Det går inte att flytta din Azure Machine Learning-arbetsyta till en annan prenumeration, eller flytta den ägande prenumerationen till en ny klientorganisation. Om du gör det kan det orsaka fel.

### <a name="deleting-the-azure-container-registry"></a>Ta bort Azure-behållarregistret

Arbetsytan Azure Machine Learning använder Azure Container Registry (ACR) för vissa åtgärder. Det kommer automatiskt att skapa en ACR-instans när den först behöver en.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nästa steg

- Utforska den fullständiga [AzureML REST API-referensen](https://docs.microsoft.com/rest/api/azureml/).
- Lär dig hur du använder Studio & Designer för att [förutsäga bilpris med designern (förhandsgranskning).](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)
- Utforska [Azure Machine Learning med Jupyter-anteckningsböcker](https://docs.microsoft.com/azure//machine-learning/samples-notebooks).
