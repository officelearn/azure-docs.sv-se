---
title: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI
description: Konfigurera meddelanderoutning för Azure IoT Hub med Azure CLI och Azure-portalen
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 38a40d628b883c0e7ada824d47d3fdf3d29caf93
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "74084375"
---
# <a name="tutorial-use-the-azure-cli-and-azure-portal-to-configure-iot-hub-message-routing"></a>Självstudiekurs: Använd Azure CLI- och Azure-portalen för att konfigurera IoT Hub-meddelanderoutning

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="use-the-azure-cli-to-create-the-base-resources"></a>Använd Azure CLI för att skapa basresurserna

Den här självstudien använder Azure CLI för att skapa basresurserna och använder sedan [Azure-portalen](https://portal.azure.com) för att visa hur du konfigurerar meddelanderoutning och konfigurerar den virtuella enheten för testning.

Kopiera och klistra in skriptet nedan i Cloud Shell och tryck på Retur. Det kör skriptet en rad i taget. Då skapas basresurserna för den här självstudien, inklusive lagringskontot, IoT Hub, Service Bus Namespace och Service Bus-kön.

Det finns flera resursnamn som måste vara globalt unika, till exempel IoT Hub-namnet och lagringskontonamnet. För att göra detta enklare läggs dessa resursnamn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. RandomValue genereras en gång högst upp i skriptet och läggs till resursnamnen efter behov i hela skriptet. Om du inte vill att det ska vara slumpmässigt kan du ställa in det på en tom sträng eller till ett visst värde.

> [!TIP]
> Ett tips om felsökning: det här skriptet använder `\`fortsättningssymbolen (omvänt snedstreck) för att göra skriptet mer läsbart. Om du har problem med att köra skriptet `bash` kontrollerar du att cloud shell-sessionen körs och att det inte finns några blanksteg efter någon av omvänt snedstreck.
>

```azurecli-interactive
# This retrieves the subscription id of the account 
#   in which you're logged in.
# This field is used to set up the routing queries.
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

Nu när basresurserna har konfigurerats kan du konfigurera meddelanderoutningen i [Azure-portalen](https://portal.azure.com).

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

### <a name="route-to-a-storage-account"></a>Vägen till ett lagringskonto

Konfigurera nu routning för lagringskontot. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här routningen har ställts in skrivs meddelanden där **egenskapen level** är inställd på **lagring** automatiskt till ett lagringskonto. 

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

1. Välj **Resursgrupper**i [Azure-portalen](https://portal.azure.com)och välj sedan din resursgrupp. I den här självstudien används **ContosoResources**.

2. Välj IoT-hubben under listan över resurser. I självstudien används **ContosoTestHub**.

3. Välj **Meddelanderoutning**. Välj +**Lägg till**i fönstret **Meddelanderoutning** . I fönstret **Lägg till en rutt** väljer du +**Lägg** till bredvid slutpunktsfältet för att visa slutpunkter som stöds, vilket visas i följande bild:

   ![Börja lägga till en slutpunkt för en rutt](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Välj **Blob-lagring**. Du ser fönstret **Lägg till en lagringsslutpunkt.**

   ![Lägga till en slutpunkt](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Ange ett namn på slutpunkten. Den här självstudien använder **ContosoStorageEndpoint**.

6. Välj **Välj Välj en behållare**. Det tar dig till en lista över dina lagringskonton. Välj det som du skapade i förberedelsesteget. I den här självstudiekursen används **contosostorage**. Den visar en lista över containrar i det lagringskontot. **Markera** den behållare som du ställer in i förberedelsestegen. I självstudien används **contosoresults**. Du går tillbaka till fönstret **Lägg till en lagringsslutpunkt** och ser de val du har gjort.

7. Ställ in kodningen på AVRO eller JSON. I den här självstudiekursen används standardinställningar för resten av fälten. Det här fältet kommer att vara nedtonat om den valda regionen inte stöder JSON-kodning.,

   > [!NOTE]
   > Du kan ange formatet för blobnamnet med hjälp av **Format på blobfilens namn**. Standardvärdet är `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formatet måste innehålla {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} och {mm} i valfri ordning.
   >
   > Exempel med standardinställt namnformat för blobfilen: om hubbnamnet är ContosoTestHub och datum/tid är 30 oktober 2018 kl. 10:56 ser blobnamnet ut så här: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Blobarna är skrivna i Avro-format.
   >

8. Välj **Skapa** om du vill skapa lagringsslutpunkten och lägga till den i flödet. Du kommer tillbaka till fönstret **Lägg till en väg**.

9. Nu slutför du resten av informationen för routningsfrågan. Den här frågan anger kriterier för att skicka meddelanden till den lagringscontainer du just lade till som en slutpunkt. Fyll i fälten på skärmen.

   **Namn**: Ange ett namn för din routningsfråga. Den här självstudien använder **ContosoStorageRoute**.

   **Slutpunkt**: visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Aktivera väg:** Se till att `enabled`det här fältet är inställt på .
   
   **Routningsfråga**: Ange `level="storage"` som frågesträng.

   ![Skapa en routningsfråga för lagringskontot](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  

   Välj **Spara**. När den är klar returneras den till fönstret meddelanderoutning där du kan se din nya routningsfråga för lagring. Stäng rutan Vägar så att du kommer tillbaka till sidan Resursgrupp.

### <a name="route-to-a-service-bus-queue"></a>Dirigera till en servicebusskö

Konfigurera nu routning för Service Bus-kön. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. När den här vägen har ställts in skrivs meddelanden där **egenskapen level** är inställd **på kritisk** till linjebusskön, vilket utlöser en Logic App, som sedan skickar ett e-postmeddelande med informationen.

1. På sidan Resursgrupp väljer du din IoT-hubb och väljer sedan **Meddelanderoutning**.

2. Välj +**Lägg till**i fönstret **Meddelanderoutning** .

3. Markera +**Lägg** till bredvid slutpunktsfältet i fönstret Lägg till **en rutt.** Välj **Service Bus-kö**. Du får fram fönstret **Lägg till Service Bus-slutpunkt**.

   ![Lägga till en slutpunkt för servicebuss](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Fyll i fälten:

   **Slutpunktsnamn**: Ange ett namn på slutpunkten. Den här självstudien använder **ContosoSBQueueEndpoint**.
   
   **Service Bus Namnområde:** Använd listrutan för att välja servicebuss namnområde som du ställer in i förberedelsestegen. I den här självstudien används **ContosoSBNamespace**.

   **Service Bus kö**: Använd listrutan för att välja servicebusskön. I den här självstudien används **contososbqueue**.

5. Välj **Skapa** om du vill lägga till ändpunkten för servicebusskön. Du kommer tillbaka till fönstret **Lägg till en väg**.

6. Nu slutför du resten av routningsfrågeinformationen. Den här frågan anger kriterierna för att skicka meddelanden till den Service Bus-kö du just lade till som en slutpunkt. Fyll i fälten på skärmen. 

   **Namn**: Ange ett namn för din routningsfråga. Den här självstudien använder **ContosoSBQueueRoute**. 

   **Slutpunkt**: visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Routningsfråga**: Ange `level="critical"` som frågesträng. 

   ![Skapa en routningsfråga för servicebusskön](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Välj **Spara**. När den återgår till Vägar-fönstret så ser du båda dina nya vägar som de visas här.

   ![De rutter du just ställt in](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Du kan se de anpassade slutpunkter som du har ställt in genom att välja fliken **Anpassade slutpunkter.**

   ![Den anpassade slutpunkten som du just ställer in](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Stäng fönstret Meddelanderoutning så att du kommer tillbaka till Resursgruppfönstret.

## <a name="create-a-simulated-device"></a>Skapa en simulerad enhet

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

## <a name="next-steps"></a>Nästa steg

Nu när du har konfigurerat resurserna och meddelandevägarna konfigurerade går du vidare till nästa självstudiekurs för att lära dig hur du skickar meddelanden till IoT-hubben och ser dem dirigeras till de olika destinationerna. 

> [!div class="nextstepaction"]
> [Del 2 - Visa resultatet för meddelanderoutning](tutorial-routing-view-message-routing-results.md)
