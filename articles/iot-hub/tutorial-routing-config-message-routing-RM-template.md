---
title: Konfigurera meddelanderoutning för Azure IoT Hub med en Azure Resource Manager-mall | Microsoft Docs
description: Konfigurera meddelanderoutning för Azure IoT Hub med en Azure Resource Manager-mall
author: robinsh
manager: philmeagit st
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 58b0039980f448c9bd953e9d59289c511ce95517
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663034"
---
# <a name="tutorial-use-an-azure-resource-manager-template-to-configure-iot-hub-message-routing"></a>Självstudier: Använda en Azure Resource Manager-mall för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="message-routing"></a>Meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

## <a name="download-the-template-and-parameters-file"></a>Ladda ned mall och parametrar-filen

För den andra delen av den här självstudiekursen ska du hämta och kör ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i den uppdatering som innehåller Azure Resource Manager-mallen och parameterfilen, samt Azure CLI och PowerShell-skript.

Gå vidare och ladda ned den [Azure IoT C# exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) nu. Packa upp filen master.zip. Resource Manager-mallen och parameterfilen är i /iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **template_iothub.json** och **template_iothub_parameters.json**.

## <a name="create-your-resources"></a>Skapa dina resurser

Du ska använda en mall för Azure Resource Manager (RM) för att skapa alla dina resurser. Azure CLI och PowerShell-skript kan köras några rader i taget. En mall i RM distribueras i ett enda steg. Den här artikeln beskrivs i avsnitt separat för att hjälpa dig att förstå var och en. Sedan den visas hur du distribuerar mallen och skapa den virtuella enheten för att testa. När mallen har distribuerats kan visa du meddelandet routningskonfiguration i portalen.

Det finns flera resursnamn som måste vara globalt unikt, till exempel IoT Hub-namn och namnet på lagringskontot. Om du vill göra namnger resurser enklare, konfigurera dessa resursnamn att lägga till ett slumpmässigt alfanumeriskt värde som genereras från aktuellt datum och tid. 

Om du tittar på mallen, ser du där variabler ser dessa resurser som använder den parameter som skickas och sammanfoga *randomValue* -parameter. 

I följande avsnitt beskrivs de parametrar som används.

### <a name="parameters"></a>Parametrar

De flesta av dessa parametrar har standardvärden. De som slutar med **_in** sammanfogas med *randomValue* så att de globalt unikt. 

**randomValue**: Det här värdet skapas från aktuellt datum och tid när du distribuerar mallen. Det här fältet är inte i parameterfilen, eftersom de genereras i själva mallen.

**subscriptionId**: Det här fältet har angetts för du till den prenumeration som du distribuerar mallen. Det här fältet är inte i parameterfilen eftersom den är inställd för dig.

**IoTHubName_in**: Det här fältet är det grundläggande namnet på IoT Hub, som är sammanfogat med randomValue vara globalt unikt.

**Plats**: Det här fältet är den Azure-region dit du distribuerar, till exempel ”westus”.

**consumer_group**: Det här fältet är konsumentgruppen för meddelanden som skickas routning slutpunkten. Den används för att filtrera resultat i Azure Steam Analytics. Det är exempelvis hela stream där får du allt, eller om du har data som skickas med consumer_group inställd **Contoso**, och du kan ställa in ett Azure Stream Analytics stream och Power BI-rapport som visar bara de posterna. Det här fältet används i del 2 av den här självstudien.

**sku_name**: Det här fältet är skalning för IoT-hubben. Det här värdet måste vara S1 eller senare; en kostnadsfri nivå fungerar inte för den här självstudiekursen eftersom det inte tillåter flera slutpunkter.

**sku_units**: Det här fältet följer med den **sku_name**, och är antalet IoT Hub-enheter som kan användas.

**d2c_partitions**: Det här fältet är antalet partitioner som används för händelseströmmen.

**storageAccountName_in**: Det här fältet är namnet på lagringskontot som ska skapas. Meddelanden dirigeras till en behållare i lagringskontot. Det här fältet är sammanfogat med randomValue att göra det unikt globalt.

**storageContainerName**: Det här fältet är namnet på behållaren där de meddelanden som dirigeras till lagringskontot lagras.

**storage_endpoint**: Det här fältet är namnet på storage-konto slutpunkten som används av den meddelanderoutning.

**service_bus_namespace_in**: Det här fältet är namnet på Service Bus-namnområde som ska skapas. Det här värdet är sammanfogat med randomValue att göra det unikt globalt.

**service_bus_queue_in**: Det här fältet är namnet på Service Bus-kö som används för att dirigera meddelanden. Det här värdet är sammanfogat med randomValue att göra det unikt globalt.

**AuthRules_sb_queue**: Det här fältet är auktoriseringsregler för service bus-kö, som används för att hämta anslutningssträngen för kön.

### <a name="variables"></a>Variabler

Dessa värden används i mallen och härleds huvudsakligen från parametrar.

**queueAuthorizationRuleResourceId**: Det här fältet är ResourceId för auktoriseringsregeln för Service Bus-kö. Resurs-ID används i sin tur för att hämta anslutningssträngen för kön.

**iotHubName**: Det här fältet är namnet på IoT Hub efter att ha randomValue sammanfogas. 

**storageAccountName**: Det här fältet är namnet på lagringskontot efter att ha randomValue sammanfogas. 

**service_bus_namespace**: Det här fältet är namnområde när randomValue sammanfogas.

**service_bus_queue**: Det här fältet är namnet på Service Bus-kön när randomValue sammanfogas.

**sbVersion**: Versionen av Service Bus-API för att använda. I det här fallet är det ”2017-04-01”.

### <a name="resources-storage-account-and-container"></a>Resurser: Lagringskontot och behållaren

Den första resursen som skapats är lagringskontot, tillsammans med den behållare som dirigeras meddelanden. Behållaren är en resurs under storage-konto. Den har en `dependsOn` sats för storage-konto, att storage-konto skapas innan behållaren.

Här är det här avsnittet ser det ut:

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

### <a name="resources-service-bus-namespace-and-queue"></a>Resurser: Service Bus-namnområde och kö

Den andra resursen som skapats är Service Bus-namnområdet, tillsammans med Service Bus-kö som dirigeras meddelanden. SKU: N anges som standard. API-versionen har hämtats från variablerna. Det är också ange att aktivera Service Bus-namnområdet när den distribuerar det här avsnittet (status: aktiva). 

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

Det här avsnittet skapar Service Bus-kö. Den här delen av skriptet har en `dependsOn` satsen som säkerställer att namnområdet har skapats innan kön.

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

### <a name="resources-iot-hub-and-message-routing"></a>Resurser: IOT Hub och meddelanderoutning

Nu när lagringskontot och Service Bus-kö har skapats kan skapa du IoT-hubben som skickar meddelanden till dem. RM-mallen använder `dependsOn` satser så att den inte försöker skapa hubben innan Service Bus-resurser och storage-kontot har skapats. 

Här är den första delen av avsnittet IoT Hub. Den här delen av mallen ställer in beroenden och börjar med egenskaper.

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

Nästa avsnitt är avsnittet för meddelande routningskonfiguration för Iot-hubben. Först är avsnittet för slutpunkter. Den här delen av mallen ställer in Routning slutpunkterna för Service Bus-kö och storage-konto, inklusive anslutningssträngar.

För att skapa anslutningssträngen för kön, behöver du queueAuthorizationRulesResourcedId, vilket är hämtade infogade. För att skapa anslutningssträngen för lagringskontot, hämtar den primära lagringsnyckeln och använda den i formatet för anslutningssträngen.

Konfigurationen av slutpunkten är också där du anger blobbformatet `AVRO` eller `JSON`.

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

Nästa avsnitt avser meddelandevägar till slutpunkterna. Det finns en uppsättning upp för varje slutpunkt, så det finns en för Service Bus-kö och en för lagringskontobehållare.

Kom ihåg att frågevillkoret för meddelanden som vidarebefordras till lagring är `level="storage"`, och frågevillkoret för meddelanden som dirigeras till Service Bus-kön är `level="critical"`.

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

Det här json visar resten av avsnittet IoT Hub, som innehåller standardinformation om- och SKU för hubben.

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

### <a name="resources-service-bus-queue-authorization-rules"></a>Resurser: Auktoriseringsregler för Service Bus-kö

Auktoriseringsregel för Service Bus-kö används för att hämta anslutningssträngen för Service Bus-kö. Den använder en `dependsOn` -satsen för att kontrollera att den inte har skapats innan du Service Bus-namnområde och Service Bus-kö.

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

I det här avsnittet skapar du en konsumentgrupp för IoT Hub-data som ska användas av Azure Stream Analytics i den andra delen av den här självstudiekursen.

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

Om du vill skicka ett värde till skriptet för distribution som ska visas, kan du använda en outputs-avsnittet. Den här delen av mallen returnerar anslutningssträngen för Service Bus-kö. Returnerar ett värde krävs inte, ingår den som ett exempel på hur du returnerar resultat till anropande skriptet.

```json
"outputs": {
    "sbq_connectionString": {
      "type": "string",
      "value": "[Concat('Endpoint=sb://',variables('service_bus_namespace'),'.servicebus.windows.net/;SharedAccessKeyName=',parameters('AuthRules_sb_queue'),';SharedAccessKey=',listkeys(variables('queueAuthorizationRuleResourceId'),variables('sbVersion')).primaryKey,';EntityPath=',variables('service_bus_queue'))]"
    }
  }
```

## <a name="deploy-the-rm-template"></a>Distribuera mallen RM

Ladda upp mallen och parameterfilen till Azure Cloud Shell för att distribuera mallen till Azure, och sedan köra ett skript för att distribuera mallen. Öppna Azure Cloud Shell och logga in. Det här exemplet används PowerShell.

Om du vill ladda upp filer, Välj den **uppladdning/nedladdning filer** ikonen i verktygsfältet och väljer sedan ladda upp.

![Cloud Shell menyraden med uppladdning/nedladdning filer markerat](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_files.png)

Använda Utforskaren som visas, för att hitta filer på den lokala hårddisken och välja dem och välj sedan **öppna**.

När filerna överförs visas en dialogruta med ett resultat något som liknar följande bild.

![Cloud Shell menyraden med uppladdning/nedladdning filer markerat](media/tutorial-routing-config-message-routing-RM-template/CloudShell_upload_results.png)

Filerna har överförts till resursen som används av din Cloud Shell-instans. 

Kör skript för att utföra distributionen. Den sista raden i det här skriptet hämtar den variabel som har ställts in att returneras--anslutningssträng för Service Bus-kö.

Dessa variabler anges i det här skriptet.

**$RGName** är resursgruppens namn som du vill distribuera mallen. Det här fältet har skapats innan du distribuerar mallen.

**$location** är den Azure-platsen som ska användas för mallen, till exempel ”westus”.

**deploymentname** är ett namn du tilldela till distributionen att hämta det returnerar värdet på variabeln.

Här är PowerShell-skriptet. Kopiera följande PowerShell-skript och klistra in den i Cloud Shell-fönstret och klicka sedan på RETUR för att köra den.

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

Om du har skriptfel kan du redigera skriptet lokalt, ladda upp den igen till Cloud Shell och kör skriptet igen. När skriptet har körts, kan du fortsätta till nästa steg.

## <a name="create-simulated-device"></a>Skapa simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har alla resurser som ställer in och meddelandevägar har konfigurerats, kan du gå vidare till nästa självstudie och lär dig hur du bearbetar och visa information om routade meddelanden.

> [!div class="nextstepaction"]
> [Del 2 – visa meddelandet routningsresultat](tutorial-routing-view-message-routing-results.md)