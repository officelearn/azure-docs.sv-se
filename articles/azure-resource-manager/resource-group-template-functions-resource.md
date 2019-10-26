---
title: Azure Resource Manager Template Functions-resurser | Microsoft Docs
description: Beskriver de funktioner som används i en Azure Resource Manager-mall för att hämta värden för resurser.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: cf791bd262849cd93a155a19ade8f8fc377f8da6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894204"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Resurs funktioner för Azure Resource Manager mallar

Resource Manager tillhandahåller följande funktioner för att hämta resurs värden:

* [extensionResourceId](#extensionresourceid)
* [lista](#list)
* [finansiär](#providers)
* [förhållande](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [prenumeration](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

För att hämta värden från parametrar, variabler eller aktuell distribution, se [distributions värde funktioner](resource-group-template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Returnerar resurs-ID för en [tilläggs resurs](extension-resource-types.md), som är en resurs typ som används på en annan resurs för att lägga till dess funktioner.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |sträng |Resurs-ID för resursen som tilläggs resursen tillämpas på. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resurs leverantörens namn område. |
| resourceName1 |Ja |sträng |Resursens namn. |
| resourceName2 |Nej |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Retur värde

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
| resourceName eller resourceIdentifier |Ja |sträng |Unikt ID för resursen. |
| apiVersion |Ja |sträng |API-version för resurs körnings tillstånd. Normalt i formatet **åååå-mm-dd**. |
| functionValues |Nej |objekt | Ett objekt som har värden för funktionen. Ange bara det här objektet för funktioner som stöder mottagning av ett objekt med parameter värden, t. ex. **listAccountSas** på ett lagrings konto. Ett exempel på att skicka funktions värden visas i den här artikeln. | 

### <a name="valid-uses"></a>Giltig användning

List funktionerna kan bara användas i egenskaperna för en resurs definition och avsnittet utdata i en mall eller distribution. När du använder med [egenskap upprepning](resource-group-create-multiple.md#property-iteration)kan du använda List funktionerna för `input` eftersom uttrycket har tilldelats till resurs egenskapen. Du kan inte använda dem med `count` eftersom antalet måste bestämmas innan List funktionen har åtgärd ATS.

### <a name="implementations"></a>Implementeringar

Den möjliga användningen av List * visas i följande tabell.

| Resurstyp | Funktions namn |
| ------------- | ------------- |
| Microsoft. AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft. AppConfiguration/configurationStores | Listnycklar |
| Microsoft. Automation/automationAccounts | [Listnycklar](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft. batch/batchAccounts | [listnycklar](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/arbets ytor/experiment/jobb | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft. blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/Channels | listChannelWithKeys |
| Microsoft. cache/Redis | [Listnycklar](/rest/api/redis/redis/listkeys) |
| Microsoft. CognitiveServices/konton | [Listnycklar](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft. ContainerRegistry/register | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft. ContainerRegistry/register | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft. ContainerRegistry/register | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/register/Webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft. ContainerRegistry/register/kör | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft. ContainerRegistry/register/uppgifter | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft. container service/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft. container service/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft. container service/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft. data-och-jobb | listCredentials |
| Microsoft. DataFactory/datafactories/gateways | listauthkeys |
| Microsoft. DataFactory/factors/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft. DataLakeAnalytics/Accounts/storageAccounts/containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft. Devices/iotHubs | [listnycklar](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft. Devices/provisioningServices/Keys | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft. Devices/provisioningServices | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft. DevTestLab/Labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft. DevTestLab/labb/scheman | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft. DevTestLab/Labs/Users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft. DevTestLab/Labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft. DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft. DocumentDB/databaseAccounts | [Listnycklar](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft. DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft. DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft. EventGrid/Domains | [Listnycklar](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft. EventGrid/ämnen | [Listnycklar](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft. EventHub/Namespaces/authorizationRules | [listnycklar](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/Namespaces/eventhubs/authorizationRules | [listnycklar](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/Jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/användare | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/användare | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft. Logic/integrationAccounts/Agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/sammansättningar | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft. Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft. Logic/integrationAccounts/Maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft. Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft. Logic/arbets flöden | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft. Logic/arbets flöden | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/arbets flöden/utlösare | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/-arbets flöden/-versioner/-utlösare | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft. MachineLearning/WebServices | [listnycklar](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/arbets ytor | listworkspacekeys |
| Microsoft. MachineLearningServices/arbets ytor/beräkningar | Listnycklar |
| Microsoft. MachineLearningServices/arbets ytor | Listnycklar |
| Microsoft. Maps/konton | [Listnycklar](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/Media Services/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/Media Services/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft. Media/Media Services/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft. Media/Media Services/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft. Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/Namespaces/authorizationRules | [listnycklar](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listnycklar](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft. OperationalInsights/arbets ytor | [Listnycklar](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft. Relay/Namespaces/authorizationRules | [listnycklar](/rest/api/relay/namespaces/listkeys) |
| Microsoft. Relay/Namespaces/disasterRecoveryConfigs/authorizationRules | listnycklar |
| Microsoft. Relay/Namespaces/HybridConnections/authorizationRules | [listnycklar](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/Namespaces/WcfRelays/authorizationRules | [listnycklar](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft. search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft. search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft. Service Bus/Namespaces/authorizationRules | [listnycklar](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft. Service Bus/Namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft. Service Bus/namnrymder/köer/authorizationRules | [listnycklar](/rest/api/servicebus/queues/listkeys) |
| Microsoft. Service Bus/namnrymder/ämnen/authorizationRules | [listnycklar](/rest/api/servicebus/topics/listkeys) |
| Microsoft. SignalRService/SignalR | [listnycklar](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listnycklar](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft. StorSimple/chefer/Devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft. StorSimple/chefer | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft. StorSimple/chefer | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| Microsoft. Web/Connections | listconsentlinks |
| Microsoft. Web/customApis | listWsdlInterfaces |
| Microsoft. Web/locations | listwsdlinterfaces |
| Microsoft. Web/apimanagementaccounts/API/Connections | listconnectionkeys |
| Microsoft. Web/apimanagementaccounts/API/Connections | listsecrets |
| Microsoft. Web/Sites/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/relays | [listnycklar](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| Microsoft. Web/Sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| Microsoft. Web/Sites/lotss/Functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

För att avgöra vilka resurs typer som har en List åtgärd har du följande alternativ:

* Visa [REST API åtgärder](/rest/api/) för en resurs leverantör och leta efter List åtgärder. Till exempel har lagrings konton [listnycklar-åtgärden](/rest/api/storagerp/storageaccounts).
* Använd cmdleten [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. I följande exempel hämtas alla List åtgärder för lagrings konton:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Använd följande Azure CLI-kommando för att filtrera endast List åtgärderna:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Retur värde

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

Andra List funktioner har olika retur format. Om du vill se formatet på en funktion tar du med den i avsnittet utdata, som du ser i exempel mal len.

### <a name="remarks"></a>Kommentarer

Ange resursen genom att antingen använda resurs namnet eller ResourceID- [funktionen](#resourceid). Använd resurs namnet när du använder en List funktion i samma mall som distribuerar den refererade resursen.

Om du använder en **list** funktion i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats. Du får ett fel meddelande om **list** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](resource-group-template-functions-logical.md#if) för en exempel mall som använder IF och list med en villkorligt distribuerad resurs.

### <a name="list-example"></a>Lista exempel

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) visas hur du returnerar de primära och sekundära nycklarna från ett lagrings konto i avsnittet utdata. Den returnerar också en SAS-token för lagrings kontot. 

Om du vill hämta SAS-token skickar du ett objekt för förfallo tiden. Förfallo tiden måste ligga i framtiden. Det här exemplet är avsett att visa hur du använder list funktionerna. Normalt använder du SAS-token i ett resurs värde i stället för att returnera det som ett utdata-värde. Utmatnings värden lagras i distributions historiken och är inte säkra.

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

## <a name="providers"></a>finansiär

```json
providers(providerNamespace, [resourceType])
```

Returnerar information om en resurs leverantör och de resurs typer som stöds. Om du inte anger någon resurs typ, returnerar funktionen alla typer som stöds för resurs leverantören.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |sträng |Namn område för providern |
| resourceType |Nej |sträng |Typ av resurs inom den angivna namn rymden. |

### <a name="return-value"></a>Retur värde

Varje typ som stöds returneras i följande format: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Mat ris ordningen för de returnerade värdena är inte garanterad.

### <a name="providers-example"></a>Exempel på leverantörer

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) visas hur du använder Provider-funktionen:

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

För resurs typen **Microsoft. Web** Resource Provider och **Sites** returnerar föregående exempel ett objekt i följande format:

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

## <a name="reference"></a>Förhållande

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Returnerar ett objekt som representerar en resurs körnings tillstånd.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Namn eller unik identifierare för en resurs. När du refererar till en resurs i den aktuella mallen anger du endast resurs namnet som en parameter. Ange resurs-ID när du refererar till en tidigare distribuerad resurs. |
| apiVersion |Nej |sträng |API-version för den angivna resursen. Ta med den här parametern när resursen inte är etablerad i samma mall. Normalt i formatet **åååå-mm-dd**. Giltiga API-versioner för din resurs finns i [referens för mallar](/azure/templates/). |
| Fullständig |Nej |sträng |Värde som anger om det fullständiga resurs objekt ska returneras. Om du inte anger `'Full'`returneras endast resursens egenskaps objekt. Det fullständiga objektet innehåller värden, till exempel resurs-ID och plats. |

### <a name="return-value"></a>Retur värde

Varje resurs typ returnerar olika egenskaper för referens funktionen. Funktionen returnerar inte ett enda, fördefinierat format. Dessutom varierar det returnerade värdet baserat på om du har angett det fullständiga objektet. Om du vill se egenskaperna för en resurs typ returnerar du objektet i avsnittet utdata som visas i exemplet.

### <a name="remarks"></a>Kommentarer

Funktionen Reference hämtar körnings status för antingen en tidigare distribuerad resurs eller en resurs som har distribuerats i den aktuella mallen. Den här artikeln innehåller exempel för båda scenarierna.

Normalt använder du funktionen **Reference** för att returnera ett visst värde från ett objekt, t. ex. blob-slutpunktens URI eller ett fullständigt kvalificerat domän namn.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Använd `'Full'` när du behöver resurs värden som inte är en del av schemat för egenskaper. Om du till exempel vill ange åtkomst principer för nyckel valv hämtar du identitets egenskaperna för en virtuell dator.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
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

Referens funktionen kan bara användas i egenskaperna för en resurs definition och avsnittet utdata i en mall eller distribution. När det används med [egenskapen iteration](resource-group-create-multiple.md#property-iteration), kan du använda funktionen reference för `input` eftersom uttrycket har tilldelats till resurs egenskapen. Du kan inte använda den med `count` eftersom antalet måste bestämmas innan referens funktionen har åtgärd ATS.

Du kan inte använda funktionen reference i utdata för en [kapslad mall](resource-group-linked-templates.md#nested-template) för att returnera en resurs som du har distribuerat i den kapslade mallen. Använd i stället en [länkad mall](resource-group-linked-templates.md#external-template).

Om du använder funktionen **Reference** i en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats.  Du får ett fel meddelande om **referens** funktionen hänvisar till en resurs som inte finns. Använd funktionen **IF** för att se till att funktionen endast utvärderas när resursen distribueras. Se [funktionen IF](resource-group-template-functions-logical.md#if) för en exempel mall som använder IF och Reference med en villkorligt distribuerad resurs.

### <a name="implicit-dependency"></a>Implicit beroende

Genom att använda referens funktionen, deklarerar du att en resurs är beroende av en annan resurs, om den refererade resursen är etablerad i samma mall och du refererar till resursen med hjälp av namnet (inte resurs-ID). Du behöver inte också använda egenskapen dependsOn. Funktionen utvärderas inte förrän den refererade resursen har slutfört distributionen.

### <a name="resource-name-or-identifier"></a>Resurs namn eller identifierare

Ange namnet på resursen när du refererar till en resurs som har distribuerats i samma mall.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ange resurs-ID när du refererar till en resurs som inte har distribuerats i samma mall.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

För att undvika tvetydighet om vilken resurs du refererar till, kan du ange ett fullständigt resurs namn.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

När du skapar en fullständigt kvalificerad referens till en resurs, är ordningen för att kombinera segment från typ och namn inte bara en sammanfogning av de två. Använd i stället en sekvens av *typnamn/namn* -par från minst för de mest aktuella för namn området:

**{Resource-Provider-namespace}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-Name}]**

Exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` är rätt `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` fel

### <a name="reference-example"></a>Referens exempel

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) distribuerar en resurs och refererar till resursen.

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

Föregående exempel returnerar de två objekten. Egenskaps-objektet har följande format:

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

Det fullständiga objektet har följande format:

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

Följande [exempel mal len refererar till](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) ett lagrings konto som inte har distribuerats i den här mallen. Lagrings kontot finns redan i samma prenumeration.

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

Returnerar ett objekt som representerar den aktuella resurs gruppen. 

### <a name="return-value"></a>Retur värde

Det returnerade objektet har följande format:

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

### <a name="remarks"></a>Kommentarer

Det går inte att använda funktionen `resourceGroup()` i en mall som har [distribuerats på prenumerations nivån](deploy-to-subscription.md). Den kan bara användas i mallar som har distribuerats till en resurs grupp.

En vanlig användning av resourceGroup-funktionen är att skapa resurser på samma plats som resurs gruppen. I följande exempel används resurs gruppens plats för att tilldela platsen för en webbplats.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

Du kan också använda funktionen resourceGroup för att lägga till taggar från resurs gruppen till en resurs. Mer information finns i [använda taggar från resurs gruppen](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Exempel på resurs grupp

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) returneras egenskaperna för resurs gruppen.

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

Returnerar den unika identifieraren för en resurs. Du använder den här funktionen när resurs namnet är tvetydigt eller inte etablerad inom samma mall. 

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nej |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceGroupName |Nej |sträng |Standardvärdet är den aktuella resurs gruppen. Ange det här värdet när du behöver hämta en resurs i en annan resurs grupp. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resurs leverantörens namn område. |
| resourceName1 |Ja |sträng |Resursens namn. |
| resourceName2 |Nej |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Retur värde

Resurs-ID: t returneras i följande format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Information om hur du hämtar ID i andra format finns i:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Kommentarer

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

Ofta måste du använda den här funktionen när du använder ett lagrings konto eller ett virtuellt nätverk i en alternativ resurs grupp. I följande exempel visas hur en resurs från en extern resurs grupp enkelt kan användas:

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

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) returnerar resurs-ID för ett lagrings konto i resurs gruppen:

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
| sameRGOutput | Sträng | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Sträng | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Sträng | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Sträng | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>prenumeration

```json
subscription()
```

Returnerar information om prenumerationen för den aktuella distributionen. 

### <a name="return-value"></a>Retur värde

Funktionen returnerar följande format:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Exempel på prenumeration

I följande [exempel mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) visas prenumerations funktionen som anropas i avsnittet utdata. 

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
| subscriptionId |Nej |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceType |Ja |sträng |Typ av resurs, inklusive resurs leverantörens namn område. |
| resourceName1 |Ja |sträng |Resursens namn. |
| resourceName2 |Nej |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Retur värde

Identifieraren returneras i följande format:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Kommentarer

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
| resourceType |Ja |sträng |Typ av resurs, inklusive resurs leverantörens namn område. |
| resourceName1 |Ja |sträng |Resursens namn. |
| resourceName2 |Nej |sträng |Nästa resurs namns segment, om det behövs. |

Fortsätt att lägga till resurs namn som parametrar när resurs typen innehåller fler segment.

### <a name="return-value"></a>Retur värde

Identifieraren returneras i följande format:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Kommentarer

Du använder den här funktionen för att hämta resurs-ID för en resurs som distribueras till klienten. Det returnerade ID: t skiljer sig från värdena som returneras av andra resurs-ID-funktioner, inklusive resurs grupps-eller prenumerations värden.

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager mall finns i [redigera Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Information om hur du sammanfogar flera mallar finns i [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Om du vill iterera ett visst antal gånger när du skapar en typ av resurs, se [skapa flera instanser av resurser i Azure Resource Manager](resource-group-create-multiple.md).
* Information om hur du distribuerar mallen som du har skapat finns i [distribuera ett program med Azure Resource Manager mall](resource-group-template-deploy.md).

