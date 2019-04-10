---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI | Microsoft Docs
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 6faa585f1ad38eb981e0bbffffef603c4aab0bc8
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360280"
---
# <a name="tutorial-use-the-azure-cli-to-configure-iot-hub-message-routing"></a>Självstudier: Använda Azure CLI för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Ladda ned skriptet (valfritt)

För den andra delen av den här självstudiekursen ska du hämta och kör ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i download som innehåller Azure Resource Manager-mallen och parameterfilen, samt Azure CLI och PowerShell-skript.

Om du vill visa det färdiga skriptet kan hämta den [Azure IoT C# exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp filen master.zip. Azure CLI-skript är i /iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **iothub_routing_cli.azcli**.

## <a name="use-the-azure-cli-to-create-your-resources"></a>Använda Azure CLI för att skapa dina resurser

Det finns flera resursnamn som måste vara globalt unikt, till exempel IoT Hub-namn och namnet på lagringskontot. Om du vill göra det enklare, läggs de resursnamn med slumpmässiga alfanumeriska värdet kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggas till för resursnamnen efter behov i skriptet. Om du inte vill att det ska vara slumpmässiga kan konfigurera du den till en tom sträng eller till ett specifikt värde. 

> [!IMPORTANT]
> Variabler i skriptet inledande används också av Routning skriptet, så körs alla skriptet i samma Cloud Shell-sessionen. Om du öppnar en ny session för att köra skript för att konfigurera routning saknar flera av variablerna värden.
>

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den körs skriptet en rad i taget. Det första avsnittet av skriptet skapar de grundläggande resurserna för den här självstudiekursen, inklusive storage-konto, IoT Hub, Service Bus Namespace och Service Bus-kö. När du går igenom resten av kursen kopierar varje block på skript och klistra in den i Cloud Shell för att köra den.

> [!TIP]
> Ett tips om hur du felsöker: det här skriptet använder symbolen fortsättning (ett omvänt snedstreck `\`) att göra det lättare att läsa skriptet. Om du har problem med att köra skriptet kan du kontrollera att det finns inga blanksteg efter någon av de omvända snedstreck.
> 

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account. 
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

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

Nu när de grundläggande resurserna har konfigurerats kan konfigurera du den meddelanderoutning.

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Du kan skapa en slutpunkt för routning med [az iot hub routning – slutpunkt skapa](/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest#az-iot-hub-routing-endpoint-create). Du kan skapa meddelandeväg för slutpunkten med [az iot hub vägen skapa](/cli/azure/iot/hub/route?view=azure-cli-latest#az-iot-hub-route-create).

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagringskonto

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Först, konfigurera slutpunkten för storage-konto och sedan konfigurera vägen. 

De här variablerna anges:

**storageConnectionString**: Det här värdet hämtas från storage-kontot som angetts i föregående skript. Den används av meddelandet routning för att komma åt lagringskontot.

  **resourceGroup**: Det finns två förekomster av resursgrupp – sätter dem på din resursgrupp.

**slutpunkten subscriptionID**: Det här fältet är inställd på Azure prenumerations-ID för slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet måste anges till `azurestoragecontainer`, `eventhub`, `servicebusqueue`, eller `servicebustopic`. För de här, ange den till `azurestoragecontainer`.

**iotHubName**: Det här fältet är namnet på hubben som kommer att göra routning.

**containerName**: Det här fältet är namnet på behållaren i lagringskontot som data ska skrivas.

**Kodning**: Det här fältet ska vara antingen `avro` eller `json`. Detta anger formatet för lagrade data.

**routeName**: Det här fältet är namnet på flödet som du ställer in. 

**endpointName**: Det här fältet är namnet som identifierar slutpunkten. 

**Aktiverad**: Standardvärdet för `true`, som anger att meddelandevägen ska aktiveras när du skapat.

**Villkoret**: Det här fältet är frågan som används för att filtrera för meddelanden som skickats till den här slutpunkten. Frågevillkoret för meddelanden som vidarebefordras till lagring är `level="storage"`.

Kopiera det här skriptet och klistra in den i Cloud Shell-fönstret och kör den.

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

Nästa steg är att skapa routning slutpunkten för storage-kontot. Du kan även ange behållaren där resultaten ska sparas. Behållaren har skapats tidigare när lagringskontot skapades.

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

Därefter skapa rutten för slutpunkt för lagring. Meddelandevägen betyder att skicka meddelanden som uppfyller frågespecifikationen. 

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

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus-kö

Konfigurera nu routning för Service Bus-kön. Du måste skapa en auktoriseringsregel som har definierats korrekt behörighet för att hämta anslutningssträngen för Service Bus-kö. Följande skript skapar en auktoriseringsregel för Service Bus-kö som kallas `sbauthrule`, och anger rättigheterna till `Listen Manage Send`. När den här auktoriseringsregeln har definierats kan använda du den för att hämta anslutningssträngen för kön.

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

Nu använda auktoriseringsregeln för att hämta anslutningssträngen till Service Bus-kö.

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

Nu ställa in Routning slutpunkten och meddelandeväg för Service Bus-kö. De här variablerna anges:

**endpointName**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet måste anges till `azurestoragecontainer`, `eventhub`, `servicebusqueue`, eller `servicebustopic`. För de här, ange den till `servicebusqueue`.

**routeName**: Det här fältet är namnet på flödet som du ställer in. 

**Villkoret**: Det här fältet är frågan som används för att filtrera för meddelanden som skickats till den här slutpunkten. Frågevillkoret för meddelanden som dirigeras till Service Bus-kön är `level="critical"`.

Här är Azure CLI för routning slutpunkten och meddelandeväg för Service Bus-kö.

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

Nu när du har de resurser som ställer in och meddelandevägar konfigurerats kan du gå vidare till nästa självstudie och lär dig hur du skickar meddelanden till IoT hub och se att de dirigeras till olika mål. 

> [!div class="nextstepaction"]
> [Del 2 – visa meddelandet routningsresultat](tutorial-routing-view-message-routing-results.md)