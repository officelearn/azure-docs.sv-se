---
title: Mall funktioner – resurser
description: Beskriver funktionerna du använder i en Azure Resource Manager-mall för att hämta värden om resurser.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b8d0a3e60654c9d3f951c6f288ea904bb4c0d50b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900644"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Resursfunktioner för Azure Resource Manager-mallar

Resource Manager tillhandahåller följande funktioner för att hämta resurs-värden:

* [extensionResourceId](#extensionresourceid)
* [lista *](#list)
* [Providers](#providers)
* [Referens](#reference)
* [resourceGroup](#resourcegroup)
* [Resurs-ID](#resourceid)
* [prenumeration](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Om du vill hämta värden från parametrar, variabler eller den aktuella distributionen, se [värdet distributionsfunktioner](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Returnerar resurs-ID för en [tilläggs resurs](../management/extension-resource-types.md), som är en resurs typ som används på en annan resurs för att lägga till dess funktioner.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |sträng |Resurs-ID för resursen som tilläggs resursen tillämpas på. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namnet på resursen. |
| resourceName2 |Inga |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Det grundläggande formatet för resurs-ID: t som returnerades av den här funktionen är:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Omfattnings segmentet varierar beroende på vilken resurs som utökas.

När tilläggs resursen används för en **resurs**returneras resurs-ID i följande format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggs resursen tillämpas på en **resurs grupp**är formatet:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggs resursen används för en **prenumeration**är formatet:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggs resursen används för en **hanterings grupp**är formatet:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId-exempel

I följande exempel returneras resurs-ID för ett resurs grupp lås.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>lista

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Syntaxen för den här funktionen varierar beroende på namnet på list åtgärderna. Varje implementering returnerar värden för den resurs typ som har stöd för en List åtgärd. Åtgärds namnet måste börja med `list`. Några vanliga användnings områden är `listKeys` och `listSecrets`. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Unik identifierare för resursen. |
| apiVersion |Ja |sträng |API-versionen av resursen runtime-tillståndet. Normalt i format, **åååå-mm-dd**. |
| functionValues |Inga |objekt | Ett objekt som har värden för funktionen. Endast ger det här objektet för funktioner som stöder tar emot ett objekt med parametervärden, exempelvis **listAccountSas** på ett lagringskonto. Ett exempel på att skicka funktions värden visas i den här artikeln. | 

### <a name="valid-uses"></a>Giltig användning

List funktionerna kan bara användas i egenskaperna för en resurs definition och avsnittet utdata i en mall eller distribution. När du använder med [egenskap upprepning](create-multiple-instances.md#property-iteration)kan du använda List funktionerna för `input` eftersom uttrycket har tilldelats till resurs egenskapen. Du kan inte använda dem med `count` eftersom antalet måste bestämmas innan List funktionen har åtgärd ATS.

### <a name="implementations"></a>Implementeringar

Den möjliga användningen av List * visas i följande tabell.

| Resurstyp | Funktions namn |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/configurationStores | Listnycklar |
| Microsoft. Automation/automationAccounts | [Listnycklar](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listnycklar](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/arbets ytor/experiment/jobb | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [Listnycklar](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [Listnycklar](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/register | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/register | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/register | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/register/kör | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/register/uppgifter | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. container service/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. container service/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. data-och-jobb | listCredentials |
| Microsoft. DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. DataShare/konton/resurser | [listSynchronizations](/rest/api/datashare/shares/listsynchronizations) | 
| Microsoft. DataShare/Accounts/shareSubscriptions | [listSourceShareSynchronizationSettings](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft. DataShare/Accounts/shareSubscriptions | [listSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft. DataShare/Accounts/shareSubscriptions | [listSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listnycklar](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/iotHubs/iotHubKeys | [listnycklar](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [Listnycklar](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/Domains | [Listnycklar](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/ämnen | [Listnycklar](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listnycklar](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listnycklar](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. Kusto/kluster/databaser | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft. LabServices/användare | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/användare | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/-arbets flöden/körningar/åtgärder | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft. Logic/-arbets flöden/körningar/åtgärder/upprepningar | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft. Logic/arbets flöden/utlösare | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listnycklar](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft. MachineLearningServices/arbets ytor/beräkningar | [Listnycklar](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft. MachineLearningServices/arbets ytor/beräkningar | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/workspaces | [Listnycklar](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft. Maps/konton | [Listnycklar](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/Media Services/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/Media Services/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/Media Services/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/Media Services/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listnycklar](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listnycklar](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/arbets ytor | [Listnycklar](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listnycklar](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listnycklar |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listnycklar](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listnycklar](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listnycklar](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listnycklar](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listnycklar](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listnycklar](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listnycklar](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| Microsoft. Web/Sites/Backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| Microsoft. Web/Sites/Functions | [listnycklar](/rest/api/appservice/webapps/listfunctionkeys)
| Microsoft. Web/Sites/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/relays | [listnycklar](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/lotss/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| Microsoft. Web/Sites/lotss/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

För att avgöra vilka resurstyper som har en liståtgärden, har du följande alternativ:

* Visa den [REST API-åtgärder](/rest/api/) för en provider för nätverksresurser och leta efter åtgärder i listan. Till exempel lagringskonton har den [Listnycklar åtgärden](/rest/api/storagerp/storageaccounts).
* Använd cmdleten [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. I följande exempel hämtas alla åtgärder i listan för storage-konton:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Använd följande Azure CLI-kommando för att filtrera endast Liståtgärder:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Returvärde

Det returnerade objektet varierar beroende på vilken List funktion du använder. Till exempel returnerar Listnycklar för ett lagrings konto följande format:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Andra listfunktioner har olika returnerade format. Om du vill se formatet för en funktion, inkludera den i outputs-avsnittet som visas i exemplet mallen.

### <a name="remarks"></a>Anmärkningar

Ange resurs med samma resurs eller [resourceId funktionen](#resourceid). Använd resurs namnet när du använder en List funktion i samma mall som distribuerar den refererade resursen.

Om du använder en **list** funktion i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats. Du får ett fel meddelande om **list** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](template-functions-logical.md#if) för en exempel mall som använder IF och list med en villkorligt distribuerad resurs.

### <a name="list-example"></a>Lista exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) visar hur du returnerar de primära och sekundära nycklarna från ett lagringskonto i outputs-avsnittet. Den returnerar också en SAS-token för storage-kontot. 

Om du vill hämta SAS-token skickar du ett objekt för förfallo tiden. Förfallo tiden måste ligga i framtiden. Det här exemplet är avsedd att visa hur du använder funktionerna lista. Normalt du skulle använda SAS-token i ett resursvärde i stället returneras som ett utdatavärde. Utdatavärden lagras i distributionshistoriken och är inte säker.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>Providers

```json
providers(providerNamespace, [resourceType])
```

Returnerar information om en resursprovider och dess resurstyper som stöds. Om du inte anger en resurstyp, returnerar funktionen typerna som stöds för resursprovidern.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |sträng |Namespace av providern |
| resourceType |Inga |sträng |Typ av resurs i det angivna namnområdet. |

### <a name="return-value"></a>Returvärde

Varje typ som stöds returneras i följande format: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matris sorteringen av de returnerade värdena är inte garanterad.

### <a name="providers-example"></a>Exempel på leverantörer

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) visar hur du använder funktionen provider:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

För den **Microsoft.Web** resursprovidern och **platser** resurstyp i föregående exempel returnerar ett objekt i följande format:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

## <a name="reference"></a>Referens

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Returnerar ett objekt som representerar en resurs runtime-tillståndet.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Namn eller unik identifierare för en resurs. När du refererar till en resurs i den aktuella mallen, anger du bara resursnamn som en parameter. Ange resurs-ID när du refererar till en tidigare distribuerad resurs eller när namnet på resursen är tvetydigt. |
| apiVersion |Inga |sträng |API-versionen av den angivna resursen. Inkludera den här parametern när resursen inte är tillhandahållits i samma mall. Normalt i format, **åååå-mm-dd**. Giltiga API-versioner för din resurs finns i [referens för mallar](/azure/templates/). |
| ”Fullständig” |Inga |sträng |Värde som anger om du vill returnera fullständiga resurs-objekt. Om du inte anger `'Full'`, egenskaper för objekt av resursen returneras. Fullständig objektet innehåller värden som resurs-ID och plats. |

### <a name="return-value"></a>Returvärde

Alla resurstyper returnerar olika egenskaper för funktionen referens. Funktionen returnerar inte ett enda, fördefinierade format. Dessutom varierar det returnerade värdet beroende på om du har angett det fullständiga objektet. Om du vill visa egenskaperna för en resurstyp, returnerar du objektet i outputs-avsnittet som visas i exemplet.

### <a name="remarks"></a>Anmärkningar

Funktionen referens hämtar körtiden för en tidigare distribuerad resurs eller en resurs som distribuerats i den aktuella mallen. Den här artikeln visar exempel på båda scenarierna.

Normalt använder du den **referens** funktionen för att returnera ett visst värde från ett objekt, till exempel blob-slutpunkt URI eller fullständigt domännamn.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Använd `'Full'` när du behöver resurs-värden som inte ingår i Egenskaper för schemat. Till exempel om du vill ställa in åtkomstprinciper för nyckelvalvet, få identitetsegenskaperna för en virtuell dator.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

### <a name="valid-uses"></a>Giltig användning

Referens-funktionen kan endast användas i egenskaperna för en resursdefinition och outputs-avsnittet av en mall eller distribution. När det används med [egenskapen iteration](create-multiple-instances.md#property-iteration), kan du använda funktionen reference för `input` eftersom uttrycket har tilldelats till resurs egenskapen. Du kan inte använda den med `count` eftersom antalet måste bestämmas innan referens funktionen har åtgärd ATS.

Du kan inte använda funktionen reference i utdata för en [kapslad mall](linked-templates.md#nested-template) för att returnera en resurs som du har distribuerat i den kapslade mallen. Använd i stället en [länkad mall](linked-templates.md#linked-template).

Om du använder funktionen **Reference** i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats.  Du får ett fel meddelande om **referens** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](template-functions-logical.md#if) för en exempel mall som använder IF och Reference med en villkorligt distribuerad resurs.

### <a name="implicit-dependency"></a>Implicit beroende

Med hjälp av funktionen referens deklarera du implicit att en resurs beror på en annan resurs om refererade resursen har tillhandahållits i samma mall och du referera till resursen med sitt namn (inte resurs-ID). Du behöver inte också använda egenskapen dependsOn. Funktionen utvärderas inte förrän den refererade resursen har slutfört distributionen.

### <a name="resource-name-or-identifier"></a>Resurs namn eller identifierare

Ange namnet på resursen när du refererar till en resurs som har distribuerats i samma mall.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ange resurs-ID när du refererar till en resurs som inte har distribuerats i samma mall.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

För att undvika tvetydighet om vilken resurs du refererar till, kan du ange en fullständigt kvalificerad resurs identifierare.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))]"
```

När du skapar en fullständigt kvalificerad referens till en resurs, är ordningen för att kombinera segment från typ och namn inte bara en sammanfogning av de två. Använd i stället en sekvens av *typnamn/namn* -par från minst för de mest aktuella för namn området:

**{Resource-Provider-namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Ett exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` är rätt `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` fel

För att förenkla skapandet av eventuella resurs-ID använder du `resourceId()` funktioner som beskrivs i det här dokumentet i stället för funktionen `concat()`.

### <a name="get-managed-identity"></a>Hämta hanterad identitet

[Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) är [tilläggs resurs typer](../management/extension-resource-types.md) som skapas implicit för vissa resurser. Eftersom den hanterade identiteten inte uttryckligen definieras i mallen måste du referera till den resurs som identiteten tillämpas på. Använd `Full` för att hämta alla egenskaper, inklusive den implicit skapade identiteten.

Om du till exempel vill hämta klient-ID: t för en hanterad identitet som används för en skalnings uppsättning för virtuella datorer använder du:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Referens exempel

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) distribuerar en resurs och refererar till den här resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Föregående exempel returnerar de två objekten. För egenskapsobjektet är i följande format:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

Fullständig objektet är i följande format:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) refererar till ett lagringskonto som inte är distribuerat i den här mallen. Lagringskontot finns redan i samma prenumeration.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

```json
resourceGroup()
```

Returnerar ett objekt som representerar den aktuella resursgruppen. 

### <a name="return-value"></a>Returvärde

Det returnerade objektet är i följande format:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

Egenskapen **managedBy** returneras bara för resurs grupper som innehåller resurser som hanteras av en annan tjänst. För hanterade program, Databricks och AKS är egenskapens värde resurs-ID för hanterings resursen.

### <a name="remarks"></a>Anmärkningar

Det går inte att använda funktionen `resourceGroup()` i en mall som har [distribuerats på prenumerations nivån](deploy-to-subscription.md). Den kan bara användas i mallar som har distribuerats till en resurs grupp. Du kan använda funktionen `resourceGroup()` i en [länkad eller kapslad mall (med inre omfång)](linked-templates.md) som är riktad mot en resurs grupp, även när den överordnade mallen distribueras till prenumerationen. I det scenariot distribueras den länkade eller kapslade mallen på resurs grupps nivå. Mer information om hur du riktar in en resurs grupp på en prenumerations nivå distribution finns i [Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp](cross-resource-group-deployment.md).

Ett vanligt användningsområde för resourceGroup-funktionen är att skapa resurser på samma plats som resursgruppen. I följande exempel används resurs grupps platsen för ett standard parameter värde.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Du kan också använda funktionen resourceGroup för att lägga till taggar från resurs gruppen till en resurs. Mer information finns i [använda taggar från resurs gruppen](../management/tag-resources.md#apply-tags-from-resource-group).

När du använder kapslade mallar för att distribuera till flera resurs grupper kan du ange omfånget för att utvärdera funktionen resourceGroup. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Exempel på resurs grupp

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) returnerar egenskaperna för resursgruppen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

Föregående exempel returnerar ett objekt i följande format:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Returnerar den unika identifieraren för en resurs. Du använder den här funktionen när resursnamnet är tvetydigt eller ej etablerad inom samma mall. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Inga |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceGroupName |Inga |sträng |Standardvärdet är aktuella resursgruppen. Ange det här värdet när du behöver hämta en resurs i en annan resursgrupp. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namnet på resursen. |
| resourceName2 |Inga |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Resurs-ID: t returneras i följande format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

När det används i en [distribution på prenumerations nivå](deploy-to-subscription.md)returneras resurs-ID i följande format:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Information om hur du hämtar ID i andra format finns i:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Anmärkningar

Antalet parametrar som du anger varierar beroende på om resursen är en överordnad eller underordnad resurs och om resursen finns i samma prenumeration eller resurs grupp.

Om du vill hämta resurs-ID för en överordnad resurs i samma prenumeration och resurs grupp anger du resursens typ och namn.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Om du vill hämta resurs-ID för en underordnad resurs, bör du tänka på antalet segment i resurs typen. Ange ett resurs namn för varje segment av resurs typen. Namnet på segmentet motsvarar den resurs som finns för den delen av hierarkin.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Om du vill hämta resurs-ID för en resurs i samma prenumeration, men i en annan resurs grupp, anger du resurs gruppens namn.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Om du vill hämta resurs-ID för en resurs i en annan prenumeration och resurs grupp anger du prenumerations-ID och resurs gruppens namn.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Du behöver ofta, Använd den här funktionen när du använder ett lagringskonto eller ett virtuellt nätverk i en annan resursgrupp. I följande exempel visas hur en resurs från en extern resursgrupp enkelt kan användas:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="resource-id-example"></a>Exempel på resurs-ID

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) returnerar resurs-ID för ett lagringskonto i resursgruppen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Utdata från föregående exempel med standardvärdena är:

| Namn | Typ | Värde |
| ---- | ---- | ----- |
| sameRGOutput | String | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | String | /subscriptions/{Current-Sub-ID}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | String | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | String | /subscriptions/{Current-Sub-ID}/resourceGroups/examplegroup/providers/Microsoft.SQL/Servers/ServerName/Databases/databaseName |

## <a name="subscription"></a>prenumeration

```json
subscription()
```

Returnerar information om prenumerationen för den aktuella distributionen. 

### <a name="return-value"></a>Returvärde

Funktionen returnerar följande format:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Anmärkningar

När du använder kapslade mallar för att distribuera till flera prenumerationer kan du ange omfattningen för utvärdering av prenumerations funktionen. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Exempel på prenumeration

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) visar anropa prenumeration-funktionen i outputs-avsnittet. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Returnerar den unika identifieraren för en resurs som distribueras på prenumerations nivå.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Inga |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namnet på resursen. |
| resourceName2 |Inga |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Identifieraren returneras i följande format:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmärkningar

Du använder den här funktionen för att hämta resurs-ID för resurser som [distribueras till prenumerationen](deploy-to-subscription.md) i stället för en resurs grupp. Det returnerade ID: t skiljer sig från värdet som returnerades av [resourceId](#resourceid) -funktionen genom att inte inkludera ett resurs grupps värde.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID-exempel

Följande mall tilldelar en inbyggd roll. Du kan distribuera den till antingen en resurs grupp eller en prenumeration. Funktionen subscriptionResourceId används för att hämta resurs-ID för inbyggda roller.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

Returnerar den unika identifieraren för en resurs som distribueras på klient nivå.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |sträng |Typ av resurs, inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namnet på resursen. |
| resourceName2 |Inga |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Identifieraren returneras i följande format:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmärkningar

Du använder den här funktionen för att hämta resurs-ID för en resurs som distribueras till klienten. Det returnerade ID: t skiljer sig från värdena som returneras av andra resurs-ID-funktioner, inklusive resurs grupps-eller prenumerations värden.

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitt i en Azure Resource Manager-mall finns i [redigera Azure Resource Manager-mallar](template-syntax.md).
* Om du vill slå samman flera mallar, se [med länkade mallar med Azure Resource Manager](linked-templates.md).
* Iterera ett angivet antal gånger när du skapar en typ av resurs, finns i [och skapa flera instanser av resurser i Azure Resource Manager](create-multiple-instances.md).
* Om du vill se hur du distribuerar mallen som du har skapat, se [distribuera ett program med Azure Resource Manager-mall](deploy-powershell.md).

