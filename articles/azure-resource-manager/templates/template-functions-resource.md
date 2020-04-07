---
title: Mallfunktioner - resurser
description: Beskriver de funktioner som ska användas i en Azure Resource Manager-mall för att hämta värden om resurser.
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 23c0463649e748b35917c959a73536147e91f60b
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744986"
---
# <a name="resource-functions-for-arm-templates"></a>Resursfunktioner för ARM-mallar

Resource Manager innehåller följande funktioner för att hämta resursvärden i din AZURE Resource Manager-mall (ARM):

* [extensionResourceId](#extensionresourceid)
* [lista*](#list)
* [Leverantörer](#providers)
* [Referens](#reference)
* [resursGrupp](#resourcegroup)
* [resourceId (resourceId)](#resourceid)
* [Prenumeration](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Om du vill hämta värden från parametrar, variabler eller den aktuella distributionen finns [i Distributionsvärdesfunktioner](template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Returnerar resurs-ID:et för en [tilläggsresurs](../management/extension-resource-types.md), som är en resurstyp som tillämpas på en annan resurs för att lägga till dess funktioner.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceId |Ja |sträng |Resurs-ID:et för resursen som tilläggsresursen tillämpas på. |
| resourceType |Ja |sträng |Typ av resurs inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namn på resurs. |
| resourceName2 |Inga |sträng |Nästa resursnamnssegment, om det behövs. |

Fortsätt att lägga till resursnamn som parametrar när resurstypen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Det grundläggande formatet för det resurs-ID som returneras av den här funktionen är:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Scopesegmentet varierar beroende på att resursen utökas.

När tilläggsresursen tillämpas på en **resurs**returneras resurs-ID:n i följande format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggsresursen tillämpas på en **resursgrupp**är formatet:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggsresursen tillämpas på en **prenumeration**är formatet:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

När tilläggsresursen tillämpas på en **hanteringsgrupp**är formatet:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>exempel på extensionResourceId

I följande exempel returneras resurs-ID:t för ett resursgrupplås.

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

## <a name="list"></a>lista*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

Syntaxen för den här funktionen varierar beroende på namnet på listoperationerna. Varje implementering returnerar värden för den resurstyp som stöder en liståtgärd. Åtgärdsnamnet måste `list`börja med . Några vanliga användningsområden är `listKeys` och `listSecrets`.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Unik identifierare för resursen. |
| apiVersion |Ja |sträng |API-version av resurskörningstillstånd. Vanligtvis, i formatet, **yyyy-mm-dd**. |
| funktionVärderar |Inga |objekt | Ett objekt som har värden för funktionen. Ange endast det här objektet för funktioner som stöder mottagande av ett objekt med parametervärden, till exempel **listAccountSas** på ett lagringskonto. Ett exempel på övergående funktionsvärden visas i den här artikeln. |

### <a name="valid-uses"></a>Giltiga användningsområden

Listfunktionerna kan bara användas i egenskaperna för en resursdefinition och utdataavsnittet i en mall eller distribution. När du används med [egenskapsiteration](copy-properties.md) `input` kan du använda listfunktionerna för eftersom uttrycket är tilldelat resursegenskapen. Du kan inte använda `count` dem med eftersom antalet måste bestämmas innan listfunktionen har lösts.

### <a name="implementations"></a>Implementeringar

Möjliga användningsområden för listan* visas i följande tabell.

| Resurstyp | Funktionsnamn |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servrar | [listaGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppKonfigurering/configurationStores | Listtangenter |
| Microsoft.Automation/automationKonton | [listTangenter](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchKonton | [listnycklar](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/arbetsytor/experiment/jobb | [listoutputfiler](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMedlem | [listaApiTangenter](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMedlem/transaktionNoder | [listaApiTangenter](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listTangenter](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/konton | [listTangenter](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.Container-register/register | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.Container-register/register | [listaCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.Container-register/register | [listaAnvändarna](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/register/webhooks | [listaHändelser](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/register/runs | [listaLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/register/uppgifter | [listDetaljer](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listaKlusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listaKlusterKredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles Microsoft.ContainerService/managedClusters/accessProfiles Microsoft.ContainerService/managedClusters/accessProfiles Microsoft. | [listaKnsiktigt](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobb | listaCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/fabriker/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listaSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/konton/resurser | [listaSynchronizations](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [listaSourceShareSynchronizationInställningar](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [listaSynchronizationDetails](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft.DataShare/accounts/shareSubscriptions Microsoft. | [listaSynchronizations](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listnycklar](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listnycklar](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft.Devices/provisioningServices Microsoft. | [listnycklar](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds (ListVhds)](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/scheman | [ListaTilläms](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics Microsoft.DevTestLab/labs/users/serviceFabrics Microsoft.DevTestLab/labs/users/serviceFabrics Microsoft. | [ListaApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListaApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databasKonton | [listaAnslutningssträngar](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databasKonton | [listTangenter](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listaDomänerKommendationer](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listaAvtal](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domäner | [listTangenter](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Ämnen för Microsoft.EventGrid/Event | [listTangenter](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listnycklar](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listnycklar](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobb | [listaBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Kluster/Databaser | [ListPrincipals](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/användare | [ListaMiljöer](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/användare | [ListLabs (ListLabs)](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationKonton/avtal | [listaContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationKonto/sammansättningar | [listaContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationKonton | [listaCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationKonton | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationKonton/kartor | [listaContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationKonton/partner | [listaContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationKonton/scheman | [listaContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/arbetsflöden | [listaCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/arbetsflöden | [listaSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listaUttrycktTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/repetitions | [listaUttrycktTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/arbetsflöden/utlösare | [listaCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/arbetsflöden/versioner/utlösare | [listaCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listnycklar](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/arbetsytor/beräkningar | [listTangenter](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/arbetsytor/beräkningar | [listaNoder](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/arbetsytor | [listTangenter](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/konton | [listTangenter](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/resurser | [listaContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/resurser | [listaStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listaInnehållstangenter](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listnycklar](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listnycklar](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/arbetsytor | [listTangenter](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listaDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listnycklar](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listnycklar |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listnycklar](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listnycklar](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listaFöramintangenter](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listnycklar](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listnycklar](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listnycklar](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listnycklar](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listnycklar](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listaAS](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listnycklar](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listaServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices Microsoft.StorSimple/managers/devices Microsoft.StorSimple/managers/devices Microsoft. | [listaFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices Microsoft.StorSimple/managers/devices Microsoft.StorSimple/managers/devices Microsoft. | [listaFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listaActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listaPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListaStatus |
| microsoft.web/anslutningar | listmedanslänkar |
| Microsoft.Web/customApis | listaWsdlInterfaces |
| microsoft.web/platser | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listanslutningsnycklar |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/backups | [list](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Webbplatser/config | [list](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listnycklar](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listnycklar](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/webbplatser | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [list](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Webbplatser/platser/config | [list](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

För att avgöra vilka resurstyper som har en liståtgärd har du följande alternativ:

* Visa [REST API-åtgärder](/rest/api/) för en resursprovider och leta efter liståtgärder. Lagringskonton har till exempel [åtgärden listKeys](/rest/api/storagerp/storageaccounts).
* Använd [Cmdleten Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell. I följande exempel får alla liståtgärder för lagringskonton:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* Använd följande Azure CLI-kommando för att filtrera endast liståtgärderna:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Returvärde

Det returnerade objektet varierar beroende på den listfunktion du använder. ListKeys för ett lagringskonto returnerar till exempel följande format:

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

Andra listfunktioner har olika returformat. Om du vill visa formatet för en funktion tar du med den i utdataavsnittet enligt exempelmallen.

### <a name="remarks"></a>Anmärkningar

Ange resursen med hjälp av resursnamnet eller [resursfunktionen](#resourceid). När du använder en listfunktion i samma mall som distribuerar den refererade resursen använder du resursnamnet.

Om du använder en **listfunktion** i en resurs som distribueras villkorligt utvärderas funktionen även om resursen inte distribueras. Du får ett felmeddelande om **listfunktionen** refererar till en resurs som inte finns. Använd **funktionen om** för att kontrollera att funktionen bara utvärderas när resursen distribueras. Se [om-funktionen](template-functions-logical.md#if) för en exempelmall som använder om och lista med en villkorligt distribuerad resurs.

### <a name="list-example"></a>Exempel på lista

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) visar hur du returnerar de primära och sekundära nycklarna från ett lagringskonto i utdataavsnittet. Den returnerar också en SAS-token för lagringskontot.

Om du vill hämta SAS-token skickar du ett objekt för utgångstiden. Utgångstiden måste vara i framtiden. Det här exemplet är avsett att visa hur du använder listfunktionerna. Vanligtvis använder du SAS-token i ett resursvärde i stället för att returnera den som ett utdatavärde. Utdatavärden lagras i distributionshistoriken och är inte säkra.

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

## <a name="providers"></a>Leverantörer

```json
providers(providerNamespace, [resourceType])
```

Returnerar information om en resursprovider och dess resurstyper som stöds. Om du inte anger någon resurstyp returnerar funktionen alla typer som stöds för resursleverantören.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| providerNamespace |Ja |sträng |Namnområde för providern |
| resourceType |Inga |sträng |Resurstypen inom det angivna namnområdet. |

### <a name="return-value"></a>Returvärde

Varje typ som stöds returneras i följande format:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Matrisordning för de returnerade värdena garanteras inte.

### <a name="providers-example"></a>Exempel på leverantörer

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) visar hur du använder providerfunktionen:

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

För **resurstypen Microsoft.Web-resurs** och **platser** returnerar föregående exempel ett objekt i följande format:

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

Returnerar ett objekt som representerar en resurs körningstillstånd.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceName eller resourceIdentifier |Ja |sträng |Namn eller unik identifierare för en resurs. När du refererar till en resurs i den aktuella mallen anger du bara resursnamnet som en parameter. När du refererar till en tidigare distribuerad resurs eller när namnet på resursen är tvetydigt anger du resurs-ID: t. |
| apiVersion |Inga |sträng |API-version av den angivna resursen. **Den här parametern krävs när resursen inte etableras i samma mall.** Vanligtvis, i formatet, **yyyy-mm-dd**. Giltiga API-versioner för resursen finns i [mallreferens](/azure/templates/). |
| "Full" |Inga |sträng |Värde som anger om hela resursobjektet ska returneras. Om du inte `'Full'`anger returneras endast egenskapsobjektet för resursen. Det fullständiga objektet innehåller värden som resurs-ID och plats. |

### <a name="return-value"></a>Returvärde

Varje resurstyp returnerar olika egenskaper för referensfunktionen. Funktionen returnerar inte ett enda, fördefinierat format. Det returnerade värdet skiljer sig också beroende `'Full'` på argumentets värde. Om du vill visa egenskaperna för en resurstyp returnerar du objektet i utdataavsnittet som visas i exemplet.

### <a name="remarks"></a>Anmärkningar

Referensfunktionen hämtar körningstillståndet för antingen en tidigare distribuerad resurs eller en resurs som distribuerats i den aktuella mallen. Den här artikeln visar exempel för båda scenarierna.

Vanligtvis använder du **referensfunktionen** för att returnera ett visst värde från ett objekt, till exempel blob-slutpunkten URI eller fullständigt kvalificerat domännamn.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Använd `'Full'` när du behöver resursvärden som inte ingår i egenskapsschemat. Om du till exempel vill ange nyckelvalvsåtkomstprinciper hämtar du identitetsegenskaperna för en virtuell dator.

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

### <a name="valid-uses"></a>Giltiga användningsområden

Referensfunktionen kan bara användas i egenskaperna för en resursdefinition och utdataavsnittet i en mall eller distribution. När du används med [egenskapsiteration](copy-properties.md) `input` kan du använda referensfunktionen för eftersom uttrycket är tilldelat resursegenskapen. Du kan inte använda `count` den med eftersom antalet måste bestämmas innan referensfunktionen har lösts.

Du kan inte använda referensfunktionen i utdata för en [kapslad mall](linked-templates.md#nested-template) för att returnera en resurs som du har distribuerat i den kapslade mallen. Använd i stället en [länkad mall](linked-templates.md#linked-template).

Om du använder **referensfunktionen** i en resurs som distribueras villkorligt utvärderas funktionen även om resursen inte distribueras.  Du får ett felmeddelande om **referensfunktionen** refererar till en resurs som inte finns. Använd **funktionen om** för att kontrollera att funktionen bara utvärderas när resursen distribueras. Se [om-funktionen](template-functions-logical.md#if) för en exempelmall som använder om och refererar till en villkorligt distribuerad resurs.

### <a name="implicit-dependency"></a>Implicit beroende

Genom att använda referensfunktionen deklarerar du implicit att en resurs är beroende av en annan resurs om den refererade resursen etableras i samma mall och du refererar till resursen med dess namn (inte resurs-ID). Du behöver inte också använda egenskapen dependsOn. Funktionen utvärderas inte förrän den refererade resursen har slutfört distributionen.

### <a name="resource-name-or-identifier"></a>Resursnamn eller identifierare

När du refererar till en resurs som distribueras i samma mall anger du namnet på resursen.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

När du refererar till en resurs som inte distribueras i samma `apiVersion`mall anger du resurs-ID och .

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Om du vill undvika tvetydighet om vilken resurs du refererar till kan du ange en fullständigt kvalificerad resursidentifierare.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

När du skapar en fullständigt kvalificerad referens till en resurs är ordningen för att kombinera segment från typen och namnet inte bara en sammanfogning av de två. Efter namnområdet använder du i stället en sekvens av *typ/namnpar* från minst specifika till mest specifika:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}] {resource-provider-namespace}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-type}/{parent-resource-**

Ett exempel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`är `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` korrekt är inte korrekt

Om du vill förenkla skapandet av `resourceId()` ett resurs-ID använder `concat()` du de funktioner som beskrivs i det här dokumentet i stället för funktionen.

### <a name="get-managed-identity"></a>Hämta hanterad identitet

[Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md) är [tilläggsresurstyper](../management/extension-resource-types.md) som skapas implicit för vissa resurser. Eftersom den hanterade identiteten inte uttryckligen definieras i mallen måste du referera till den resurs som identiteten tillämpas på. Används `Full` för att hämta alla egenskaper, inklusive den implicit skapade identiteten.

Om du till exempel vill hämta klient-ID:t för en hanterad identitet som tillämpas på en skaluppsättning för virtuella datorer använder du:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Exempel på referens

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) distribuerar en resurs och refererar till resursen.

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

I föregående exempel returneras de två objekten. Egenskapsobjektet är i följande format:

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

Det fullständiga objektet är i följande format:

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

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) refererar ett lagringskonto som inte distribueras i den här mallen. Lagringskontot finns redan i samma prenumeration.

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

Egenskapen **managedBy** returneras endast för resursgrupper som innehåller resurser som hanteras av en annan tjänst. För hanterade program, Databricks och AKS är värdet för egenskapen resurs-ID för hanteringsresursen.

### <a name="remarks"></a>Anmärkningar

Funktionen `resourceGroup()` kan inte användas i en mall som [distribueras på prenumerationsnivå](deploy-to-subscription.md). Den kan bara användas i mallar som distribueras till en resursgrupp. Du kan `resourceGroup()` använda funktionen i en [länkad eller kapslad mall (med inre omfattning)](linked-templates.md) som riktar sig till en resursgrupp, även när den överordnade mallen distribueras till prenumerationen. I det fallet distribueras den länkade eller kapslade mallen på resursgruppsnivå. Mer information om hur du inriktar dig på en resursgrupp i en prenumerationsnivådistribution finns i [Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](cross-resource-group-deployment.md).

En vanlig användning av resursgruppsfunktionen är att skapa resurser på samma plats som resursgruppen. I följande exempel används resursgruppsplatsen för ett standardparametervärde.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Du kan också använda resursgruppsfunktionen för att använda taggar från resursgruppen på en resurs. Mer information finns i [Använda taggar från resursgruppen](../management/tag-resources.md#apply-tags-from-resource-group).

När du använder kapslade mallar för att distribuera till flera resursgrupper kan du ange omfånget för utvärdering av resursgruppsfunktionen. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](cross-resource-group-deployment.md).

### <a name="resource-group-example"></a>Exempel på resursgrupp

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) returneras resursgruppens egenskaper.

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

I föregående exempel returneras ett objekt i följande format:

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

Returnerar den unika identifieraren för en resurs. Du använder den här funktionen när resursnamnet är tvetydigt eller inte etablerat i samma mall. Formatet på den returnerade identifieraren varierar beroende på om distributionen sker i omfattningen av en resursgrupp, prenumeration, hanteringsgrupp eller klient.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Inga |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. Ange endast det här värdet när du distribuerar i omfattningen av en resursgrupp eller prenumeration. |
| resourceGroupName |Inga |sträng |Standardvärdet är aktuell resursgrupp. Ange det här värdet när du behöver hämta en resurs i en annan resursgrupp. Ange endast det här värdet när du distribuerar i en resursgrupps omfattning. |
| resourceType |Ja |sträng |Typ av resurs inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namn på resurs. |
| resourceName2 |Inga |sträng |Nästa resursnamnssegment, om det behövs. |

Fortsätt att lägga till resursnamn som parametrar när resurstypen innehåller fler segment.

### <a name="return-value"></a>Returvärde

När mallen distribueras i en resursgrupps omfattning returneras resurs-ID:n i följande format:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

När [resurs-ID](deploy-to-subscription.md)används i en prenumerationsnivå returneras resurs-ID i följande format:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

När resurs-ID används i en [distribution på hanteringsgruppsnivå](deploy-to-management-group.md) eller distribution på klientnivå returneras resurs-ID i följande format:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Så här hämtar du ID:et i andra format:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Anmärkningar

Antalet parametrar som du anger varierar beroende på om resursen är en överordnad eller underordnad resurs och om resursen finns i samma prenumeration eller resursgrupp.

Om du vill hämta resurs-ID:n för en överordnad resurs i samma prenumerations- och resursgrupp anger du resursens typ och namn.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Om du vill hämta resurs-ID:et för en underordnad resurs bör du vara uppmärksam på antalet segment i resurstypen. Ange ett resursnamn för varje segment i resurstypen. Namnet på segmentet motsvarar den resurs som finns för den delen av hierarkin.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Om du vill hämta resurs-ID:n för en resurs i samma prenumeration men en annan resursgrupp anger du resursgruppsnamnet.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Om du vill hämta resurs-ID:n för en resurs i en annan prenumerations- och resursgrupp anger du prenumerations-ID: n och resursgruppsnamnet.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Ofta måste du använda den här funktionen när du använder ett lagringskonto eller ett virtuellt nätverk i en alternativ resursgrupp. I följande exempel visas hur en resurs från en extern resursgrupp enkelt kan användas:

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

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) returneras resurs-ID:t för ett lagringskonto i resursgruppen:

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
| olikaRGOutput | Sträng | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Sträng | /subscriptions/1111111-1111-1111-1111-111111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Sträng | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

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

När du använder kapslade mallar för att distribuera till flera prenumerationer kan du ange omfånget för utvärdering av prenumerationsfunktionen. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](cross-resource-group-deployment.md).

### <a name="subscription-example"></a>Exempel på prenumeration

I följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) visas prenumerationsfunktionen som anropas i utdataavsnittet.

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

Returnerar den unika identifieraren för en resurs som distribueras på prenumerationsnivå.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| subscriptionId |Inga |sträng (i GUID-format) |Standardvärdet är den aktuella prenumerationen. Ange det här värdet när du behöver hämta en resurs i en annan prenumeration. |
| resourceType |Ja |sträng |Typ av resurs inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namn på resurs. |
| resourceName2 |Inga |sträng |Nästa resursnamnssegment, om det behövs. |

Fortsätt att lägga till resursnamn som parametrar när resurstypen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Identifieraren returneras i följande format:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmärkningar

Du använder den här funktionen för att hämta resurs-ID:et för resurser som [distribueras till prenumerationen](deploy-to-subscription.md) i stället för en resursgrupp. Det returnerade ID:et skiljer sig från värdet som returneras av [resursfunktionen Genom](#resourceid) att inte inkludera ett resursgruppsvärde.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID exempel

Följande mall tilldelar en inbyggd roll. Du kan distribuera den till antingen en resursgrupp eller prenumeration. Den använder funktionen subscriptionResourceId för att hämta resurs-ID för inbyggda roller.

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

Returnerar den unika identifieraren för en resurs som distribueras på klientnivå.

### <a name="parameters"></a>Parametrar

| Parameter | Krävs | Typ | Beskrivning |
|:--- |:--- |:--- |:--- |
| resourceType |Ja |sträng |Typ av resurs inklusive resursproviderns namnområde. |
| resourceName1 |Ja |sträng |Namn på resurs. |
| resourceName2 |Inga |sträng |Nästa resursnamnssegment, om det behövs. |

Fortsätt att lägga till resursnamn som parametrar när resurstypen innehåller fler segment.

### <a name="return-value"></a>Returvärde

Identifieraren returneras i följande format:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Anmärkningar

Du använder den här funktionen för att hämta resurs-ID:et för en resurs som distribueras till klienten. Det returnerade ID:t skiljer sig från de värden som returneras av andra resurs-ID-funktioner genom att inte inkludera resursgrupps- eller prenumerationsvärden.

## <a name="next-steps"></a>Nästa steg

* En beskrivning av avsnitten i en Azure Resource Manager-mall finns i [Redigera Azure Resource Manager-mallar](template-syntax.md).
* Information om hur du sammanfogar flera mallar finns [i Använda länkade mallar med Azure Resource Manager](linked-templates.md).
* Information om hur du itererar ett angivet antal gånger när du skapar en typ av resurs finns [i Skapa flera instanser av resurser i Azure Resource Manager](copy-resources.md).
* Information om hur du distribuerar mallen som du har skapat finns i [Distribuera ett program med Azure Resource Manager-mallen](deploy-powershell.md).

