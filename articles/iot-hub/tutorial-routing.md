---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI och Azure portal | Microsoft Docs
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI och Azure portal
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 2f382c31c6bfb6ab71afd495c4c3f702715633c0
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661896"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Självstudier: Använd Azure CLI och Azure-portalen för att konfigurera IoT Hub meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Använda Azure CLI för att skapa de grundläggande resurserna

Den här självstudien används Azure CLI för att skapa de grundläggande resurserna och använder sedan den [Azure-portalen](https://portal.azure.com) att visa hur du konfigurerar meddelanderoutning och konfigurera den virtuella enheten för att testa.

Det finns flera resursnamn som måste vara globalt unikt, till exempel IoT Hub-namn och namnet på lagringskontot. Om du vill göra det enklare, läggs de resursnamn med slumpmässiga alfanumeriska värdet kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggas till för resursnamnen efter behov i skriptet. Om du inte vill att det ska vara slumpmässiga kan konfigurera du den till en tom sträng eller till ett specifikt värde.

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på RETUR. Den körs skriptet en rad i taget. Detta skapar de grundläggande resurserna för den här självstudiekursen, inklusive storage-konto, IoT Hub, Service Bus Namespace och Service Bus-kö.

En notering om felsökning: det här skriptet använder symbolen fortsättning (ett omvänt snedstreck `\`) att göra det lättare att läsa skriptet. Om du har problem med att köra skriptet kan du kontrollera att det finns inga blanksteg efter någon av de omvända snedstreck.

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing rules.
subscriptionID=$(az account show --query id)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# Set the values for the resource names that 
#   don't have to be globally unique.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults

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

```

Nu när de grundläggande resurserna har konfigurerats kan du konfigurera meddelanderoutning i den [Azure-portalen](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Dirigera till ett lagringskonto

Konfigurera nu routning för lagringskontot. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här routning är konfigurerat meddelanden var den **nivå** är inställd på **storage** skrivs till ett lagringskonto automatiskt. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. I den [Azure-portalen](https://portal.azure.com)väljer **resursgrupper**, välj sedan din resursgrupp. I den här självstudien används **ContosoResources**.

2. Välj IoT-hubben under listan över resurser. I självstudien används **ContosoTestHub**.

3. Välj **meddelanderoutning**. I den **meddelanderoutning** väljer +**Lägg till**. På den **lägga till en väg** väljer +**Lägg till** bredvid fältet slutpunkt för att visa slutpunkter som stöds som det visas i följande bild:

   ![Börja lägga till en slutpunkt för en väg](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Välj **Blob-lagring**. Du ser den **lägga till en slutpunkt för lagring** fönstret.

   ![Att lägga till en slutpunkt](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Ange ett namn på slutpunkten. Den här självstudien används **ContosoStorageEndpoint**.

6. Välj **Välj en behållare**. Det tar dig till en lista över dina lagringskonton. Välj det som du skapade i förberedelsesteget. I den här självstudiekursen används **contosostorage**. Den visar en lista över containrar i det lagringskontot. **Välj** behållaren du skapade i steg för förberedelse. I självstudien används **contosoresults**. Du kommer tillbaka till den **lägga till en slutpunkt för lagring** fönstret och se de val du gjort.

7. Ange kodning till AVRO eller JSON. I den här självstudiekursen används standardinställningar för resten av fälten. Det här fältet nedtonade om den valda regioner inte har stöd för JSON-kodning.,

   > [!NOTE]
   > Du kan ange formatet för blobnamnet med hjälp av **Format på blobfilens namn**. Standardvärdet är `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formatet måste innehålla {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} och {mm} i valfri ordning.
   >
   > Exempel med standardinställt namnformat för blobfilen: om hubbnamnet är ContosoTestHub och datum/tid är 30 oktober 2018 kl. 10:56 ser blobnamnet ut så här: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Blobarna är skrivna i Avro-format.
   >

8. Välj **skapa** skapa slutpunkt för lagring och lägga till den vägen. Du kommer tillbaka till fönstret **Lägg till en väg**.

9. Nu slutför du resten av informationen för routningsfrågan. Den här frågan anger kriterier för att skicka meddelanden till den lagringscontainer du just lade till som en slutpunkt. Fyll i fälten på skärmen.

   **Namn**: Ange ett namn för din routningsfråga. Den här självstudien används **ContosoStorageRoute**.

   **Slutpunkt**: Visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Aktivera rutt**: Tänk på det här fältet är inställt på `enabled`.
   
   **Dirigeringsfråga**: Ange `level="storage"` som frågesträng.

   ![Skapar en fråga för routning för storage-konto](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Välj **Spara**. När den är klar returneras den till fönstret meddelanderoutning där du kan se din nya routningsfråga för lagring. Stäng rutan Vägar så att du kommer tillbaka till sidan Resursgrupp.

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en Service Bus-kö

Konfigurera nu routning för Service Bus-kön. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här vägen har konfigurerats meddelanden var den **nivå** är inställd på **kritiska** skrivs till Service Bus-kö som utlöser en Logikapp som skickar ett e-postmeddelande med information.

1. Välj din IoT-hubb på resursgruppsidan, och sedan **meddelanderoutning**.

2. I den **meddelanderoutning** väljer +**Lägg till**.

3. På den **lägga till en väg** fönstret Välj +**Lägg till** bredvid fältet slutpunkt. Välj **Service Bus-kö**. Du får fram fönstret **Lägg till Service Bus-slutpunkt**.

   ![Att lägga till en service bus-slutpunkt](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Fyll i fälten:

   **Slutpunktsnamn**: Ange ett namn på slutpunkten. Den här självstudien används **ContosoSBQueueEndpoint**.
   
   **Service Bus-namnområde**: Använd listrutan för att välja service bus-namnområde som du skapade i steg för förberedelse. I den här självstudien används **ContosoSBNamespace**.

   **Service Bus-kö**: Använd listrutan för att välja Service Bus-kö. I den här självstudien används **contososbqueue**.

5. Välj **skapa** att lägga till slutpunkten för Service Bus-kö. Du kommer tillbaka till fönstret **Lägg till en väg**.

6. Nu slutför du resten av routningsfrågeinformationen. Den här frågan anger kriterierna för att skicka meddelanden till den Service Bus-kö du just lade till som en slutpunkt. Fyll i fälten på skärmen. 

   **Namn**: Ange ett namn för din routningsfråga. Den här självstudien används **ContosoSBQueueRoute**. 

   **Slutpunkt**: Visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Dirigeringsfråga**: Ange `level="critical"` som frågesträng. 

   ![Skapa en fråga som routning för Service Bus-kö](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Välj **Spara**. När den återgår till Vägar-fönstret så ser du båda dina nya vägar som de visas här.

   ![De vägar som du precis har konfigurerat](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Du kan se de anpassade slutpunkter som du ställer in genom att välja den **anpassade slutpunkter** fliken.

   ![Anpassad slutpunkt som du precis har konfigurerat](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Stäng fönstret Meddelanderoutning så att du kommer tillbaka till Resursgruppfönstret.

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create- imulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har de resurser som ställer in och meddelandevägar konfigurerats kan du gå vidare till nästa självstudie och lär dig hur du skickar meddelanden till IoT hub och se att de dirigeras till olika mål. 

> [!div class="nextstepaction"]
> [Del 2 – visa meddelandet routningsresultat](tutorial-routing-view-message-routing-results.md)
