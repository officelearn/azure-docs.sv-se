---
title: Konfigurera meddelanderoutning för Azure IoT Hub med hjälp av en Azure Resource Manager-mall
description: Konfigurera meddelanderoutning för Azure IoT Hub med hjälp av en Azure Resource Manager-mall
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8f245653a8b84944e1e8a3f48a49992f0065be58
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084393"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Självstudiekurs: Använda en Azure Resource Manager-mall för att konfigurera IoT Hub-meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Meddelanderoutning

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Ladda ner mall- och parameterfilen

För den andra delen av den här självstudien hämtar och kör du ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i den hämtningen som innehåller Mall- och parameterfilen i Azure Resource Manager samt Azure CLI- och PowerShell-skript.

Ladda ned [Azure IoT C#-samplingarna](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) nu. Packa upp filen master.zip. Mallen Resurshanteraren och parameterfilen finns i /iot-hub/Tutorials/Routing/SimulatedDevice/resources/ som **template_iothub.json** och **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Skapa dina resurser

Du ska använda en RM-mall (Azure Resource Manager) för att skapa alla dina resurser. Azure CLI- och PowerShell-skripten kan köras några rader åt gången. En RM-mall distribueras i ett steg. I den här artikeln visas avsnitten separat för att hjälpa dig att förstå var och en. Då kommer det att visa dig hur du distribuerar mallen och skapa den virtuella enheten för testning. När mallen har distribuerats kan du visa konfigurationen för meddelanderoutning i portalen.

Det finns flera resursnamn som måste vara globalt unika, till exempel IoT Hub-namnet och lagringskontonamnet. För att göra det enklare att namnge resurserna ställs dessa resursnamn in för att lägga till ett slumpmässigt alfanumeriskt värde som genereras från aktuellt datum/tid. 

Om du tittar på mallen ser du var variabler ställs in för dessa resurser som tar parametern som skickas in och sammanfogar *randomValue* till parametern. 

I följande avsnitt beskrivs de parametrar som används.

### <a name="parameters"></a>Parametrar

De flesta av dessa parametrar har standardvärden. De som slutar med **_in** är sammanfogade med *randomValue* för att göra dem globalt unika. 

**randomValue**: Det här värdet genereras från aktuellt datum/tid när du distribuerar mallen. Det här fältet finns inte i parameterfilen, eftersom det genereras i själva mallen.

**subscriptionId**: Det här fältet är inställt för dig till den prenumeration som du distribuerar mallen till. Det här fältet finns inte i parameterfilen eftersom det är inställt för dig.

**IoTHubName_in**: Det här fältet är basnamnet IoT Hub, som sammanfogas med randomValue för att vara globalt unikt.

**plats:** Det här fältet är den Azure-region som du distribuerar till, till exempel "westus".

**consumer_group**: Det här fältet är konsumentgruppen inställd för meddelanden som kommer via routningslutpunkten. Den används för att filtrera resultat i Azure Stream Analytics. Det finns till exempel hela strömmen där du får allt, eller om du har data som kommer igenom med consumer_group inställd på **Contoso**, kan du ställa in en Azure Stream Analytics-ström (och Power BI-rapport) för att bara visa dessa poster. Det här fältet används i del 2 i den här självstudien.

**sku_name**: Det här fältet är skalningen för IoT Hub. Detta värde måste vara S1 eller högre. En kostnadsfri nivå fungerar inte för den här självstudien eftersom den inte tillåter flera slutpunkter.

**sku_units**: Det här fältet går till **sku_name**och är antalet IoT Hub-enheter som kan användas.

**d2c_partitions**: Det här fältet är antalet partitioner som används för händelseströmmen.

**storageAccountName_in**: Det här fältet är namnet på det lagringskonto som ska skapas. Meddelanden dirigeras till en behållare i lagringskontot. Det här fältet är sammanfogat med randomValue för att göra det globalt unikt.

**storageContainerName**: Det här fältet är namnet på den behållare där meddelandena som dirigeras till lagringskontot lagras.

**storage_endpoint**: Det här fältet är namnet på slutpunkten för lagringskontot som används av meddelanderoutningen.

**service_bus_namespace_in**: Det här fältet är namnet på det servicebussnamnområde som ska skapas. Det här värdet sammanfogas med randomValue för att göra det globalt unikt.

**service_bus_queue_in**: Det här fältet är namnet på den servicebusskö som används för routningsmeddelanden. Det här värdet sammanfogas med randomValue för att göra det globalt unikt.

**AuthRules_sb_queue**: Det här fältet är auktoriseringsreglerna för servicebusskön som används för att hämta anslutningssträngen för kön.

### <a name="variables"></a>Variabler

Dessa värden används i mallen och härleds oftast från parametrar.

**queueAuthorizationRuleResourceId**: Det här fältet är ResourceId för auktoriseringsregeln för servicebusskön. ResourceId används i sin tur för att hämta anslutningssträngen för kön.

**iotHubName**: Det här fältet är namnet på IoT Hub efter att ha randomValue sammanfogade. 

**storageAccountName**: Det här fältet är namnet på lagringskontot efter att ha randomValue sammanfogat. 

**service_bus_namespace**: Det här fältet är namnområdet efter att ha randomValue sammanfogat.

**service_bus_queue**: Det här fältet är könamnet Service Bus efter att ha randomValue sammanfogats.

**sbVersion**: THe version av Service Bus API att använda. I det här fallet är det "2017-04-01".

### <a name="resources-storage-account-and-container"></a>Resurser: Lagringskonto och behållare

Den första resursen som skapas är lagringskontot, tillsammans med behållaren som meddelanden dirigeras till. Behållaren är en resurs under lagringskontot. Den har `dependsOn` en sats för lagringskontot, som kräver att lagringskontot skapas före behållaren.

Så här ser det här avsnittet ut:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "properties": {},
    "resources": [
        {
        "type": "blobServices/containers",
        "apiVersion": "2018-07-01",
        "name": "[concat('default/', parameters('storageContainerName'))]",
        "properties": {
            "publicAccess": "None"
            } ,
        "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
            ]
        }
    ]
}
```

### <a name="resources-service-bus-namespace-and-queue"></a>Resurser: Service Bus namnområde och kö

Den andra resursen som skapas är servicebussnamnområdet, tillsammans med den servicebusskö som meddelanden dirigeras till. SKU:n är inställd på standard. API-versionen hämtas från variablerna. Det är också inställt på att aktivera Service Bus-namnområdet när det distribuerar det här avsnittet (status:Aktiv). 

```json
{
    "type": "Microsoft.ServiceBus/namespaces",
    "comments": "The Sku should be 'Standard' for this tutorial.",
    "sku": {
        "name": "Standard",
        "tier": "Standard"
    },
    "name": "[variables('service_bus_namespace')]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "properties": {
        "provisioningState": "Succeeded",
        "metricId": "[concat('a4295411-5eff-4f81-b77e-276ab1ccda12:', variables('service_bus_namespace'))]",
        "serviceBusEndpoint": "[concat('https://', variables('service_bus_namespace'),'.servicebus.windows.net:443/')]",
        "status": "Active"
    },
    "dependsOn": []
}
```

I det här avsnittet skapas köen Service Bus. Den här delen av `dependsOn` skriptet har en sats som säkerställer att namnområdet skapas före kön.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {},
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]"
    ]
}
```

### <a name="resources-iot-hub-and-message-routing"></a>Resurser: Iot Hub och meddelanderoutning

Nu när lagringskontot och Service Bus-kön har skapats skapar du IoT Hub som dirigerar meddelanden till dem. RM-mallen `dependsOn` använder satser så att den inte försöker skapa navet innan Service Bus-resurserna och lagringskontot har skapats. 

Här är den första delen av IoT Hub-avsnittet. Den här delen av mallen ställer in beroenden och börjar med egenskaperna.

```json
{
    "apiVersion": "2018-04-01",
    "type": "Microsoft.Devices/IotHubs",
    "name": "[variables('IoTHubName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ],
    "properties": {
        "eventHubEndpoints": {}
            "events": {
                "retentionTimeInDays": 1,
                "partitionCount": "[parameters('d2c_partitions')]"
                }
            },
```

Nästa avsnitt är avsnittet för meddelanderoutningskonfigurationen för Iot Hub. Först är avsnittet för slutpunkterna. Den här delen av mallen ställer in routningsslutpunkterna för servicebusskön och lagringskontot, inklusive anslutningssträngarna.

Om du vill skapa anslutningssträngen för kön behöver du köAukuktoriseringRulesResourcedId, som hämtas infogad. Om du vill skapa anslutningssträngen för lagringskontot hämtar du den primära lagringsnyckeln och använder den sedan i formatet för anslutningssträngen.

Slutpunktskonfigurationen är också där du `AVRO` ställer `JSON`in blob-formatet på eller .

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

 ```json
"routing": {
    "endpoints": {
        "serviceBusQueues": [
        {
            "connectionString": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]",
            "name": "[parameters('service_bus_queue_endpoint')]",
            "subscriptionId": "[parameters('subscriptionId')]", 
            "resourceGroup": "[resourceGroup().Name]"
        }
        ],
        "serviceBusTopics": [],
        "eventHubs": [],
        "storageContainers": [
            {
                "connectionString": 
                "[Concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value)]",
                "containerName": "[parameters('storageContainerName')]",
                "fileNameFormat": "{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}",
                "batchFrequencyInSeconds": 100,
                "maxChunkSizeInBytes": 104857600,
                "encoding": "avro",
                "name": "[parameters('storage_endpoint')]",
                "subscriptionId": "[parameters('subscriptionId')]",
                "resourceGroup": "[resourceGroup().Name]"
            }
        ]
    },
```

Det här nästa avsnittet är för meddelandevägarna till slutpunkterna. Det finns en uppsättning för varje slutpunkt, så det finns en för servicebusskön och en för lagringskontobehållaren.

Kom ihåg att frågevillkoret för de meddelanden `level="storage"`som dirigeras till lagring är och frågevillkoret `level="critical"`för de meddelanden som dirigeras till servicebusskön är .

```json
"routes": [
    {
        "name": "contosoStorageRoute",
        "source": "DeviceMessages",
        "condition": "level=\"storage\"",
        "endpointNames": [
            "[parameters('storage_endpoint')]"
            ],
        "isEnabled": true
    },
    {
        "name": "contosoSBQueueRoute",
        "source": "DeviceMessages",
        "condition": "level=\"critical\"",
        "endpointNames": [
            "[parameters('service_bus_queue_endpoint')]"
            ],
        "isEnabled": true
    }
],
```

Den här json visar resten av IoT Hub-avsnittet, som innehåller standardinformation och SKU för navet.

```json
            "fallbackRoute": {
                "name": "$fallback",
                "source": "DeviceMessages",
                "condition": "true",
                "endpointNames": [
                    "events"
                ],
                "isEnabled": true
            }
        },
        "storageEndpoints": {
            "$default": {
                "sasTtlAsIso8601": "PT1H",
                "connectionString": "",
                "containerName": ""
            }
        },
        "messagingEndpoints": {
            "fileNotifications": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        },
        "enableFileUploadNotifications": false,
        "cloudToDevice": {
            "maxDeliveryCount": 10,
            "defaultTtlAsIso8601": "PT1H",
            "feedback": {
                "lockDurationAsIso8601": "PT1M",
                "ttlAsIso8601": "PT1H",
                "maxDeliveryCount": 10
            }
        }
    },
    "sku": {
        "name": "[parameters('sku_name')]",
        "capacity": "[parameters('sku_units')]"
    }
}
```

### <a name="resources-service-bus-queue-authorization-rules"></a>Resurser: Behörighetsregler för tjänstbusskö

Behörighetsregeln för servicebusskö används för att hämta anslutningssträngen för servicebusskön. Den använder `dependsOn` en sats för att säkerställa att den inte skapas före servicebussens namnområde och servicebusskön.

```json
{
    "type": "Microsoft.ServiceBus/namespaces/queues/authorizationRules",
    "name": "[concat(variables('service_bus_namespace'), '/', variables('service_bus_queue'), '/', parameters('AuthRules_sb_queue'))]",
    "apiVersion": "[variables('sbVersion')]",
    "location": "[parameters('location')]",
    "scale": null,
    "properties": {
        "rights": [
            "Send"
        ]
    },
    "dependsOn": [
        "[resourceId('Microsoft.ServiceBus/namespaces', variables('service_bus_namespace'))]",
        "[resourceId('Microsoft.ServiceBus/namespaces/queues', variables('service_bus_namespace'), variables('service_bus_queue'))]"
    ]
},
```

### <a name="resources-consumer-group"></a>Resurser: Konsumentgrupp

I det här avsnittet skapar du en konsumentgrupp för IoT Hub-data som ska användas av Azure Stream Analytics i den andra delen av den här självstudien.

```json
{
    "type": "Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups",
    "name": "[concat(variables('iotHubName'), '/events/',parameters('consumer_group'))]",
    "apiVersion": "2018-04-01",
    "dependsOn": [
        "[concat('Microsoft.Devices/IotHubs/', variables('iotHubName'))]"
    ]
}
```

### <a name="resources-outputs"></a>Resurser: Utdata

Om du vill skicka tillbaka ett värde till distributionsskriptet som ska visas använder du ett utdataavsnitt. Den här delen av mallen returnerar anslutningssträngen för servicebusskön. Det krävs inget att returnera ett värde som ett exempel på hur du returnerar resultat till det anropande skriptet.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Distribuera RM-mallen

Om du vill distribuera mallen till Azure laddar du upp mallen och parameterfilen till Azure Cloud Shell och kör sedan ett skript för att distribuera mallen. Öppna Azure Cloud Shell och logga in. I det här exemplet används PowerShell.

Om du vill ladda upp filerna väljer du ikonen **Ladda upp/ladda ned filer** i menyraden och väljer sedan Ladda upp.

![Cloud Shell menyrad med Ladda upp / Ladda ner filer markerade](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Använd Utforskaren som dyker upp för att hitta filerna på den lokala disken och markera dem och välj sedan **Öppna**.

När filerna har laddats upp visar en resultatdialogruta ungefär följande bild.

![Cloud Shell menyrad med Ladda upp / Ladda ner filer markerade](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Filerna överförs till den resurs som används av din Cloud Shell-instans. 

Kör skriptet för att utföra distributionen. Den sista raden i det här skriptet hämtar variabeln som har ställts in för att returneras – anslutningssträngen för servicebusskön.

Skriptet anger och använder dessa variabler:

**$RGName** är det resursgruppsnamn som mallen ska distribueras till. Det här fältet skapas innan mallen distribueras.

**$location** är den Azure-plats som ska användas för mallen, till exempel "westus".

**distributionsnamn** är ett namn som du tilldelar distributionen för att hämta det returnerande variabelvärdet.

Här är PowerShell-skriptet. Kopiera det här PowerShell-skriptet och klistra in det i Cloud Shell-fönstret och tryck sedan på Retur för att köra det.

```powershell
$RGName="ContosoResources"
$location = "westus"
$deploymentname="contoso-routing"

# Remove the resource group if it already exists. 
#Remove-AzResourceGroup -name $RGName 
# Create the resource group.
New-AzResourceGroup -name $RGName -Location $location 

# Set a path to the parameter file. 
$parameterFile = "$HOME/template_iothub_parameters.json"
$templateFile = "$HOME/template_iothub.json"

# Deploy the template.
New-AzResourceGroupDeployment `
    -Name $deploymentname `
    -ResourceGroupName $RGName `
    -TemplateParameterFile $parameterFile `
    -TemplateFile $templateFile `
    -verbose

# Get the returning value of the connection string.
(Get-AzResourceGroupDeployment -ResourceGroupName $RGName -Name $deploymentname).Outputs.sbq_connectionString.value
```

Om du har skriptfel kan du redigera skriptet lokalt, ladda upp det igen till Cloud Shell och köra skriptet igen. När skriptet har körts fortsätter du till nästa steg.

## <a name="create-simulated-device"></a>Skapa simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat alla resurser och meddelandevägarna är konfigurerade går du vidare till nästa självstudiekurs för att lära dig hur du bearbetar och visar informationen om de dirigerade meddelandena.

> [!div class="nextstepaction"]
> [Del 2 - Visa resultatet för meddelanderoutning](tutorial-routing-view-message-routing-results.md)
