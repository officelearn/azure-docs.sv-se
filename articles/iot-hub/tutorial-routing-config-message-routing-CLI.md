---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI. Beroende på egenskaperna i meddelandet går du till antingen ett lagringskonto eller en Service Bus-kö.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 056dac7977115f97892d8dbfde0710e00237804e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78674344"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Självstudiekurs: Använd Azure CLI för att konfigurera IoT Hub-meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Ladda ner skriptet (valfritt)

För den andra delen av den här självstudien hämtar och kör du ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i hämtningen som innehåller Mall- och parameterfilen i Azure Resource Manager samt Azure CLI- och PowerShell-skript.

Om du vill visa det färdiga skriptet laddar du ned [Azure IoT C# Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp filen master.zip. Azure CLI-skriptet finns i /iot-hub/tutorials/routning/simulateddevice/resources/ som **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Använda Azure CLI för att skapa dina resurser

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på Retur. Det kör skriptet en rad i taget. I det här första avsnittet i skriptet skapas basresurserna för den här självstudien, inklusive lagringskontot, IoT Hub, Service Bus Namespace och Service Bus-kön. När du går igenom resten av självstudien, kopiera varje block av skript och klistra in den i Cloud Shell för att köra den.

> [!TIP]
> Ett tips om felsökning: det här skriptet använder `\`fortsättningssymbolen (omvänt snedstreck) för att göra skriptet mer läsbart. Om du har problem med att köra skriptet `bash` kontrollerar du att cloud shell-sessionen körs och att det inte finns några blanksteg efter någon av omvänt snedstreck.
> 

Det finns flera resursnamn som måste vara globalt unika, till exempel IoT Hub-namnet och lagringskontonamnet. För att göra detta enklare läggs dessa resursnamn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue genereras en gång högst upp i skriptet och läggs till resursnamnen efter behov i hela skriptet. Om du inte vill att det ska vara slumpmässigt kan du ställa in det på en tom sträng eller till ett visst värde. 

> [!IMPORTANT]
> Variablerna som anges i det ursprungliga skriptet används också av routningsskriptet, så kör alla skript i samma Cloud Shell-session. Om du öppnar en ny session för att köra skriptet för att ställa in operationsföljden, saknas flera av variablerna värden.
>

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
iotHubName=ContosoTestHub$randomValue 
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub for the 'events' endpoint.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, 
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# The Service Bus namespace must be globally unique, 
#   so add a random value to the end.
sbNamespace=ContosoSBNamespace$randomValue
echo "Service Bus namespace = " $sbNamespace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNamespace \
    --location $location

# The Service Bus queue name must be globally unique, 
#   so add a random value to the end.
sbQueueName=ContosoSBQueue$randomValue
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNamespace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName
```

Nu när basresurserna har ställts in kan du konfigurera meddelanderoutningen.

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Om du vill skapa en routningsslutpunkt använder du [az iot hub routing-endpoint create](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Om du vill skapa meddelandevägen för slutpunkten använder du [az iot hub route create](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Vägen till ett lagringskonto

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Ställ först in slutpunkten för lagringskontot och ställ sedan in flödet. 

Det här är de variabler som används av skriptet som måste anges i cloud shell-sessionen:

**storageConnectionString**: Det här värdet hämtas från lagringskontot som ställts in i föregående skript. Den används av meddelanderoutningen för att komma åt lagringskontot.

  **resourceGroup**: Det finns två förekomster av resursgruppen – ange dem till resursgruppen.

**endpoint subscriptionID**: Det här fältet är inställt på Azure subscriptionID för slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet `azurestoragecontainer`måste `eventhub` `servicebusqueue`anges `servicebustopic`till , , eller . För dina syften här, `azurestoragecontainer`ställ in den på .

**iotHubName**: Det här fältet är namnet på det nav som ska utföra routningen.

**containerName**: Det här fältet är namnet på behållaren i lagringskontot som data ska skrivas till.

**kodning:** Det här fältet `avro` kommer `json`att vara antingen eller . Detta betecknar formatet på lagrade data.

**routeName**: Det här fältet är namnet på den väg som du ställer in. 

**slutpunktNamn**: Det här fältet är namnet som identifierar slutpunkten. 

**aktiverad**: Det här `true`fältet är som standard , vilket anger att meddelandevägen ska aktiveras efter att ha skapats.

**villkor:** Det här fältet är den fråga som används för att filtrera för de meddelanden som skickas till den här slutpunkten. Frågevillkoret för de meddelanden som dirigeras till lagring är `level="storage"`.

Kopiera skriptet och klistra in det i cloud shell-fönstret och kör det.

```azurecli
##### ROUTING FOR STORAGE ##### 

endpointName="ContosoStorageEndpoint"
endpointType="azurestoragecontainer"
routeName="ContosoStorageRoute"
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)
```

Nästa steg är att skapa routningslutpunkten för lagringskontot. Du anger också behållaren där resultaten ska lagras. Behållaren skapades tidigare när lagringskontot skapades.

```azurecli
# Create the routing endpoint for storage.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName \
  --resource-group $resourceGroup \
  --encoding avro
```

Skapa sedan flödet för lagringsslutpunkten. Meddelandevägen anger var de meddelanden som uppfyller frågespecifikationen ska skickas. 

```azurecli
# Create the route for the storage endpoint.
az iot hub route create \
  --name $routeName \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
```

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en servicebusskö

Konfigurera nu routning för Service Bus-kön. Om du vill hämta anslutningssträngen för servicebusskön måste du skapa en auktoriseringsregel som har rätt rättigheter definierade. Följande skript skapar en auktoriseringsregel `sbauthrule`för den tjänstbusskö som anropas och anger rättigheterna till `Listen Manage Send`. När den här auktoriseringsregeln har definierats kan du använda den för att hämta anslutningssträngen för kön.

```azurecli
# Create the authorization rule for the Service Bus queue.
az servicebus queue authorization-rule create \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --rights Listen Manage Send \
  --subscription $subscriptionID
```

Använd nu auktoriseringsregeln för att hämta anslutningssträngen till servicebusskön.

```azurecli
# Get the Service Bus queue connection string.
# The "-o tsv" ensures it is returned without the default double-quotes.
sbqConnectionString=$(az servicebus queue authorization-rule keys list \
  --name "sbauthrule" \
  --namespace-name $sbNamespace \
  --queue-name $sbQueueName \
  --resource-group $resourceGroup \
  --subscription $subscriptionID \
  --query primaryConnectionString -o tsv)

# Show the Service Bus queue connection string.
echo "service bus queue connection string = " $sbqConnectionString
```

Ställ nu in routningsslutpunkten och meddelandevägen för servicebusskön. Det här är de variabler som används av skriptet som måste anges i cloud shell-sessionen:

**slutpunktNamn**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet `azurestoragecontainer`måste `eventhub` `servicebusqueue`anges `servicebustopic`till , , eller . För dina syften här, `servicebusqueue`ställ in den på .

**routeName**: Det här fältet är namnet på den väg som du ställer in. 

**villkor:** Det här fältet är den fråga som används för att filtrera för de meddelanden som skickas till den här slutpunkten. Frågevillkoret för de meddelanden som dirigeras till `level="critical"`servicebusskön är .

Här är Azure CLI för routningslutpunkten och meddelandevägen för servicebusskön.

```azurecli
endpointName="ContosoSBQueueEndpoint"
endpointType="ServiceBusQueue"
routeName="ContosoSBQueueRoute"
condition='level="critical"'

# Set up the routing endpoint for the Service Bus queue.
# This uses the Service Bus queue connection string.
az iot hub routing-endpoint create \
  --connection-string $sbqConnectionString \
  --endpoint-name $endpointName \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --resource-group $resourceGroup 

# Set up the message route for the Service Bus queue endpoint.
az iot hub route create --name $routeName \
  --hub-name $iotHubName \
  --source-type devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName \
  --enabled \
  --condition $condition
  ```

### <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelandevägarna konfigurerade går du vidare till nästa självstudiekurs för att lära dig hur du skickar meddelanden till IoT-hubben och ser dem dirigeras till de olika destinationerna. 

> [!div class="nextstepaction"]
> [Del 2 - Visa resultatet för meddelanderoutning](tutorial-routing-view-message-routing-results.md)
