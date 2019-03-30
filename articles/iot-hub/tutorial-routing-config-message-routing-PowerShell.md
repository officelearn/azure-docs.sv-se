---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure PowerShell | Microsoft Docs
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure PowerShell
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 51e9bc85c2ee843aa096674a25a1f634bd08b838
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660953"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>Självstudier: Använda Azure PowerShell för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>Ladda ned skriptet (valfritt)

För den andra delen av den här självstudiekursen ska du hämta och kör ett Visual Studio-program för att skicka meddelanden till IoT Hub. Det finns en mapp i download som innehåller Azure Resource Manager-mallen och parameterfilen, samt Azure CLI och PowerShell-skript. 

Om du vill visa det färdiga skriptet kan hämta den [Azure IoT C# exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp filen master.zip. Azure CLI-skript är i /iot-hub/Tutorials/Routing/SimulatedDevice/resources/som **iothub_routing_psh.ps1**.

## <a name="create-your-resources"></a>Skapa dina resurser

Börja med att skapa resurser med PowerShell.

### <a name="use-powershell-to-create-your-base-resources"></a>Använd PowerShell för att skapa din grundläggande resurser

Det finns flera resursnamn som måste vara globalt unikt, till exempel IoT Hub-namn och namnet på lagringskontot. Om du vill göra det enklare, läggs de resursnamn med slumpmässiga alfanumeriska värdet kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggas till för resursnamnen efter behov i skriptet. Om du inte vill att det ska vara slumpmässiga kan konfigurera du den till en tom sträng eller till ett specifikt värde. 

> [!IMPORTANT]
> Variabler i skriptet inledande används också av Routning skriptet, så körs alla skriptet i samma Cloud Shell-sessionen. Om du öppnar en ny session för att köra skript för att konfigurera routning saknar flera av variablerna värden. 
>

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den körs skriptet en rad i taget. Det första avsnittet av skriptet skapar de grundläggande resurserna för den här självstudiekursen, inklusive storage-konto, IoT Hub, Service Bus Namespace och Service Bus-kö. När du går igenom kursen kopiera varje block på skript och klistra in den i Cloud Shell för att köra den.

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

Nu när de grundläggande resurserna har konfigurerats kan konfigurera du den meddelanderoutning.

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

Du kan skapa en slutpunkt för routning med [Lägg till AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint). Använd för att skapa meddelanden vägen för slutpunkten [Lägg till AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute).

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagringskonto 

Först konfigurera slutpunkten för storage-konto och sedan skapa meddelandevägen.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

De här variablerna anges:

**resourceGroup**: Det finns två förekomster av det här fältet – Ange båda i resursgruppen.

**name**: Det här fältet är namnet på IoT-hubben som routning ska gälla.

**endpointName**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet måste anges till `azurestoragecontainer`, `eventhub`, `servicebusqueue`, eller `servicebustopic`. För de här, ange den till `azurestoragecontainer`.

**subscriptionID**: Det här fältet är inställt på prenumerations-ID för ditt Azure-konto.

**storageConnectionString**: Det här värdet hämtas från storage-kontot som angetts i föregående skript. Den används av routning för att få åtkomst till lagringskontot.

**containerName**: Det här fältet är namnet på behållaren i lagringskontot som data ska skrivas.

**Encoding**: Ange fältet till antingen `AVRO` eller `JSON`. Detta anger formatet för lagrade data. Standardvärdet är AVRO.

**routeName**: Det här fältet är namnet på flödet som du ställer in. 

**Villkoret**: Det här fältet är frågan som används för att filtrera för meddelanden som skickats till den här slutpunkten. Frågevillkoret för meddelanden som vidarebefordras till lagring är `level="storage"`.

**Aktiverad**: Standardvärdet för `true`, som anger att meddelandevägen ska aktiveras när du skapat.

Kopiera det här skriptet och klistra in den i Cloud Shell-fönstret.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

Nästa steg är att skapa routning slutpunkten för storage-kontot. Du kan även ange behållaren där resultaten ska sparas. Behållaren har skapats när lagringskontot skapades.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

Därefter skapa meddelandeväg för slutpunkt för lagring. Meddelandevägen betyder att skicka meddelanden som uppfyller frågespecifikationen.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus-kö

Konfigurera nu routning för Service Bus-kön. Du måste skapa en auktoriseringsregel som har definierats korrekt behörighet för att hämta anslutningssträngen för Service Bus-kö. Följande skript skapar en auktoriseringsregel för Service Bus-kö som kallas `sbauthrule`, och anger rättigheterna till `Listen Manage Send`. När den här auktoriseringsregeln har ställts in, kan du använda den för att hämta anslutningssträngen för kön.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

Nu använda auktoriseringsregeln kan hämta nyckel för Service Bus-kö. Den här auktoriseringsregeln används för att hämta anslutningssträngen senare i skriptet.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

Nu ställa in Routning slutpunkten och meddelandeväg för Service Bus-kö. De här variablerna anges:

**endpointName**: Det här fältet är namnet som identifierar slutpunkten. 

**endpointType**: Det här fältet är typen av slutpunkt. Det här värdet måste anges till `azurestoragecontainer`, `eventhub`, `servicebusqueue`, eller `servicebustopic`. För de här, ange den till `servicebusqueue`.

**routeName**: Det här fältet är namnet på flödet som du ställer in. 

**Villkoret**: Det här fältet är frågan som används för att filtrera för meddelanden som skickats till den här slutpunkten. Frågevillkoret för meddelanden som dirigeras till Service Bus-kön är `level="critical"`.

Här är Azure PowerShell för meddelanderoutning för Service Bus-kö.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>Visa meddelanderoutning i portalen

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har de resurser som ställer in och meddelandevägar konfigurerats kan du gå vidare till nästa självstudie och lär dig hur du skickar meddelanden till IoT hub och se att de dirigeras till olika mål. 

> [!div class="nextstepaction"]
> [Del 2 – visa meddelandet routningsresultat](tutorial-routing-view-message-routing-results.md)