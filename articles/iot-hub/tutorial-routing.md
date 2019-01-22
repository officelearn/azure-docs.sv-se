---
title: Konfigurera meddelandedirigering med Azure IoT Hub (.NET) | Microsoft Docs
description: Konfigurera meddelandedirigering med Azure IoT Hub
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 09/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 22678a44cda38b52982e977281d3310efde15831
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247290"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Självstudie: Konfigurera meddelandedirigering med IoT Hub

[Meddelanderoutning](iot-hub-devguide-messages-d2c.md) låter dig skicka telemetridata från dina IoT-enheter till inbyggda händelsehubbkompatibla slutpunkter som Blob-lagring, Service Bus-kö, Service Bus-ämne och Event Hubs. När du konfigurerar meddelanderoutning så kan du skapa [routningsfrågor](iot-hub-devguide-routing-query-syntax.md) för att anpassa den väg som matchar ett visst villkor. Därefter dirigeras inkommande data automatiskt till slutpunkterna av IoT Hub. 

I den här självstudien får du lära dig att konfigurera och använda routningsfrågor med IoT Hub. Du kommer att dirigera meddelanden från en IoT-enhet till en av flera tjänster, däribland Blob Storage och en Service Bus-kö. Meddelanden till Service Bus-kön hämtas av en logikapp och skickas via e-post. Meddelanden som inte har konfigurerade inställningar för routning skickas till standardslutpunkten och visas i en Power BI-visualisering.

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Konfigurera grundläggande resurser med Azure CLI eller PowerShell – en IoT-hubb, ett lagringskonto, en Service Bus-kö och en simulerad enhet.
> * Konfigurera slutpunkter och vägar i IoT Hub för lagringskontot och Service Bus-kön.
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Installera [Visual Studio](https://www.visualstudio.com/). 

- Ett Power BI-konto för att analysera Stream Analytics för standardslutpunkten. ([Prova Power BI utan kostnad](https://app.powerbi.com/signupredirect?pbi_source=web).)

- Ett Office 365-konto för att skicka e-postmeddelanden. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Konfigurera resurser

För den är självstudien använder du en IoT-hubb, ett lagringskonto och en Service Bus-kö. De här resurserna kan skapas med Azure CLI eller Azure PowerShell. Använd samma resursgrupp och plats för alla resurser. I slutet kan du ta bort allt i ett steg genom att ta bort resursgruppen.

Följande avsnitt beskriver hur du utför stegen som krävs. Följ instruktionerna för CLI *eller* PowerShell.

1. Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md). 

2. Skapa en IoT-hubb på S1-nivån. Lägg till en konsumentgrupp till din IoT-hubb. Konsumentgruppen används av Azure Stream Analytics när data hämtas.

   > [!NOTE]
   > Du måste använda en Iot-hubb på en betalnivå för att slutföra den här självstudien. På den kostnadsfria nivån kan bara ställa in en slutpunkt, och den här självstudien kräver flera slutpunkter.
   > 

3. Skapa ett V1-standardlagringskonto med Standard_LRS-replikering.

4. Skapa ett namnområde och en kö för Service Bus. 

5. Skapa en enhetsidentitet för den simulerade enheten som skickar meddelanden till din hubb. Spara nyckeln för testfasen.

### <a name="set-up-your-resources-using-azure-cli"></a>Konfigurera resurser med Azure CLI

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad. 

`$RANDOM` har sammanfogats med de variabler som måste vara globalt unika. När skriptet körs och variablerna har angetts genereras en slumpmässig numerisk sträng som sammanfogas i slutet av den fasta strängen så att den blir unik.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
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
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="set-up-your-resources-using-azure-powershell"></a>Konfigurera resurser med Azure PowerShell

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad.

`$(Get-Random)` har sammanfogats med de variabler som måste vara globalt unika. När skriptet körs och variablerna har angetts genereras en slumpmässig numerisk sträng som sammanfogas i slutet av den fasta strängen så att den blir unik.

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Skapa sedan en enhetsidentitet och spara nyckeln för framtida bruk. Den är enhetsidentiteten används av simuleringsprogrammet för att skicka meddelanden till IoT-hubben. Den här funktionen finns inte i PowerShell, men du kan skapa enheten i [Azure-portalen](https://portal.azure.com).

1. Öppna [Azure-portalen](https://portal.azure.com) och logga in på ditt Azure-konto.

2. Klicka på **Resursgrupper** och välj din resursgrupp. I den här självstudien används **ContosoResources**.

3. I listan över resurser klickar du på din IoT-hubb. I självstudien används **ContosoTestHub**. Välj **IoT-enheter** från hubbfönstret.

4. Klicka på **+ Lägg till**. I rutan Lägg till enhet fyller du i enhets-ID. I den här självstudien används **Contoso-Test-Device**. Lämna nycklarna tomma och markera **Generera nycklar automatiskt**. Kontrollera att **Connect device to IoT hub** (Anslut enhet till IoT-hubb) är aktiverat. Klicka på **Spara**.

   ![Skärmbild som visar skärmen Lägg till enhet.](./media/tutorial-routing/add-device.png)

5. Nu när den har skapats klickar du på enheten för att se de genererade nycklarna. Klicka på ikonen Kopiera på primärnyckeln och spara den någonstans, till exempel i Anteckningar, för testfasen i den här självstudien.

   ![Skärmbild som visar information om enheten, som nycklarna.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Konfigurera meddelanderedigering

Du kommer att dirigera meddelanden till olika resurser baserat på egenskaper som är anslutna till meddelandet av den simulerade enheten. Meddelanden som inte är dirigeringsanpassade skickas till standardslutpunkten (meddelanden/händelser). 

|värde |Resultat|
|------|------|
|level="storage" |Skriv till Azure Storage.|
|level="critical" |Skriv till en Service Bus-kö. En logikapp hämtar meddelandet från kön och använder Office 365 för att skicka meddelandet via e-post.|
|standard |Visa dessa data med Power BI.|

### <a name="routing-to-a-storage-account"></a>Dirigera till ett lagringskonto 

Konfigurera nu routning för lagringskontot. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. Meddelanden där egenskapen **nivå** är inställd på **lagring** skrivs automatiskt till ett lagringskonto. 

Data skrivs till bloblagring i Avro-format.

1. I [Azure-portalen](https://portal.azure.com) klickar du på **Resursgrupper** och väljer resursgruppen. I den här självstudien används **ContosoResources**. 

2. Klicka på IoT-hubben i listan över resurser. I självstudien används **ContosoTestHub**. 

3. Klicka på **Meddelanderoutning**. I fönstret **Meddelanderoutning**, klickar du på +**Lägg till**. På fönstret **Lägg till en väg**, klickar du på +**Lägg till** bredvid fältet slutpunkt som visas i följande bild:

   ![Skärmbild som visar hur du börjar lägga till en slutpunkt till en väg.](./media/tutorial-routing/message-routing-add-a-route-w-storage-ep.png)

4. Välj **Blob-lagring**. Du får fram fönstret **Lägg till slutpunkt för lagring**. 

   ![Skärmbild som visar hur du lägger till en slutpunkt.](./media/tutorial-routing/message-routing-add-storage-ep.png)

5. Ange ett namn på slutpunkten. I den här självstudien används **StorageContainer**.

6. Klicka på **Välj en container**. Det tar dig till en lista över dina lagringskonton. Välj det som du skapade i förberedelsesteget. I den här självstudien används **contosostorage**. Den visar en lista över containrar i det lagringskontot. Välj den container som du skapade i förberedelsesteget. I självstudien används **contosoresults**. Klicka på **Välj**. Du kommer tillbaka till fönstret **Lägg till slutpunkt**. 

7. I den här självstudien används standardinställningar för resten av fälten. 

   > [!NOTE]
   > Du kan ange formatet för blobnamnet med hjälp av **Format på blobfilens namn**. Standardvärdet är `{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}`. Formatet måste innehålla {iothub}, {partition}, {YYYY}, {MM}, {DD}, {HH} och {mm} i valfri ordning. 
   > 
   > Exempel med standardinställt namnformat för blobfilen: om hubbnamnet är ContosoTestHub och datum/tid är 30 oktober 2018 kl. 10:56 ser blobnamnet ut så här: `ContosoTestHub/0/2018/10/30/10/56`.
   > 
   > Blobarna är skrivna i Avro-format.
   >

8. Klicka på **Skapa** för att skapa slutpunkten för lagring och lägga till den till vägen. Du kommer tillbaka till fönstret **Lägg till en väg**.

9. Nu slutför du resten av informationen för routningsfrågan. Den här frågan anger kriterier för att skicka meddelanden till den lagringscontainer du just lade till som en slutpunkt. Fyll i fälten på skärmen. 

   **Namn**: Ange ett namn för din routningsfråga. I den här självstudien används **StorageRoute**.

   **Slutpunkt**: Visar den slutpunkt som du nyss skapade. 
   
   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Aktivera rutt**: Se till att detta är aktiverat.
   
   **Dirigeringsfråga**: Ange `level="storage"` som frågesträng. 

   ![Skärmbild som visar hur du skapar en hanteringsregel för lagringskontot.](./media/tutorial-routing/message-routing-finish-route-storage-ep.png)  
   
   Klicka på **Spara**. När den är klar returneras den till fönstret meddelanderoutning där du kan se din nya routningsfråga för lagring. Stäng rutan Vägar så att du kommer tillbaka till sidan Resursgrupp.

### <a name="routing-to-a-service-bus-queue"></a>Dirigera till en Service Bus-kö 

Konfigurera nu routning för Service Bus-kön. Du gå till fönstret meddelanderoutning och lägger till en väg. När du lägger till vägen, definierar du en ny slutpunkt för den. Efter att det här har konfigurerats så skrivs meddelanden där egenskapen **nivå** är inställd på **kritisk** till Service Bus-kön, som utlöser en Logic App som i sin tur skickar ett e-postmeddelande med informationen. 

1. På Resursgruppsidan klickar du på din IoT-hubb och sedan på **Meddelanderoutning**. 

2. I fönstret **Meddelanderoutning**, klickar du på +**Lägg till**. 

3. På fönstret **Lägg till en väg**, klickar på +**Lägg till** bredvid fältet slutpunkt. Välj **Service Bus-kö**. Du får fram fönstret **Lägg till Service Bus-slutpunkt**. 

   ![Skärmbild lägga till en service bus-slutpunkt](./media/tutorial-routing/message-routing-add-sbqueue-ep.png)

4. Fyll i fälten:

   **Slutpunktsnamn**: Ange ett namn på slutpunkten. Den här självstudien använder **CriticalQueue**.
   
   **Service Bus-namnområde**: Klicka på fältet för att visa listmenyn, välj det service bus-namnområde som du skapade i förberedelsesteget. I den här självstudien används **ContosoSBNamespace**.

   **Service Bus-kö**: Klicka på fältet för att visa listmenyn, välj service bus-kö från listmenyn. I den här självstudien används **contososbqueue**.

5. Klicka på **Skapa** för att lägga till slutpunkten för Service Bus-kön. Du kommer tillbaka till fönstret **Lägg till en väg**. 

6.  Nu slutför du resten av routningsfrågeinformationen. Den här frågan anger kriterierna för att skicka meddelanden till den Service Bus-kö du just lade till som en slutpunkt. Fyll i fälten på skärmen. 

   **Namn**: Ange ett namn för din routningsfråga. Den här självstudien använder **SBQueueRoute**. 

   **Slutpunkt**: Visar den slutpunkt som du nyss skapade.

   **Datakälla**: Välj **Enhetstelemetrimeddelanden** från listrutan.

   **Dirigeringsfråga**: Ange `level="critical"` som frågesträng. 

   ![Skärmbild som visar hur du skapar en routningsfråga för Service Bus-kön.](./media/tutorial-routing/message-routing-finish-route-sbq-ep.png)

7. Klicka på **Spara**. När den återgår till Vägar-fönstret så ser du båda dina nya vägar som de visas här.

   ![Skärmbild som visar de vägar du just konfigurerade.](./media/tutorial-routing/message-routing-show-both-routes.png)

8. Du kan se de anpassade slutpunkter som du har konfigurerat genom att klicka på fliken **Anpassade slutpunkter**.

   ![Skärmbild som visar de anpassade slutpunkter du just konfigurerade.](./media/tutorial-routing/message-routing-show-custom-endpoints.png)

9. Stäng fönstret Meddelanderoutning så att du kommer tillbaka till Resursgruppfönstret.

## <a name="create-a-logic-app"></a>Skapa en logikapp  

Service Bus-kön ska användas för att ta emot meddelanden som har angetts som kritiska. Konfigurera en logikapp som ska övervaka Service Bus-kön och som skickar e-post när ett meddelande läggs till i Service Bus-kön. 

1. I [Azure-portalen](https://portal.azure.com) klickar du på **+ Skapa en resurs**. Skriv **logikapp** i sökrutan och klicka på Retur. Från sökresultatet som visas väljer du Logikapp och klickar på **Skapa** för att fortsätta till rutan **Skapa en logikapp**. Fyll i fälten. 

   **Namn**: Det här fältet är logikappens namn. I självstudien används **ContosoLogicApp**. 

   **Prenumeration**: Välj din Azure-prenumeration.

   **Resursgrupp**: Klicka på **Använd befintlig** och välj din resursgrupp. I den här självstudien används **ContosoResources**. 

   **Plats**: Använd din plats. I den här självstudien används **USA, västra**. 

   **Log Analytics**: Den här växeln bör vara avstängd. 

   ![Skärmbild som visar skärmen Skapa en logikapp.](./media/tutorial-routing/create-logic-app.png)

   Klicka på **Skapa**.

2. Gå nu till logikappen. Det enklaste sättet att komma till logikappen är att klicka på **Resursgrupper**, välja din resursgrupp (den här självstudien använder **ContosoResources**) och sedan välja logikappen från listan över resurser. Sidan Logikappdesigner visas (du kanske måste rulla över till höger för att se hela sidan). På sidan Logikappdesigner rullar du ned tills du ser en ruta med texten **Tom logikapp +** och klickar på den. 

3. En lista med anslutningsprogram visas. Välj **Service Bus**. 

   ![Skärmbild som visar listan över anslutningsprogram.](./media/tutorial-routing/logic-app-connectors.png)

4. En lista med utlösare visas. Välj **Service Bus – När ett meddelande tas emot i en kö (komplettera automatiskt)**. 

   ![Skärmbild som visar listan över utlösare för Service Bus.](./media/tutorial-routing/logic-app-triggers.png)

5. Fyll i namnet på anslutningen på nästa skärm. I den här självstudien används **ContosoConnection**. 

   ![Skärmbild som visar konfiguration av anslutningen för Service Bus-kön.](./media/tutorial-routing/logic-app-define-connection.png)

   Klicka på Service Bus-namnområdet. I den här självstudien används **ContosoSBNamespace**. När du väljer namnrymd ber portalen Service Bus-namnrymden att hämta nycklarna. Välj **RootManageSharedAccessKey** och klicka på **Skapa**. 
   
   ![Skärmbild som visar slutförandet av konfigurationen av anslutningen.](./media/tutorial-routing/logic-app-finish-connection.png)

6. På nästa skärm väljer du namnet på kön (den här självstudien använder **contososbqueue**) i listrutan. Du kan använda standardvärdena för resten av fälten. 

   ![Skärmbild som visar köalternativen.](./media/tutorial-routing/logic-app-queue-options.png)

7. Konfigurera nu att åtgärden ska skicka ett e-postmeddelande när ett meddelande kommer till kön. I Logikappsdesignern klickar du på **+ Nytt steg** för att lägga till ett steg och sedan på **Lägg till en åtgärd**. I rutan **Välj en åtgärd** söker du upp och klickar på **Office 365 Outlook**. På skärmen med utlösare väljer du **Office 365 Outlook – Skicka ett e-postmeddelande**.  

   ![Skärmbild som visar Office365-alternativen.](./media/tutorial-routing/logic-app-select-outlook.png)

8. Logga sedan in på ditt Office 365-konto för att konfigurera anslutningen. Ange e-postadresser för mottagare av e-postmeddelanden. Ange även ämne och skriv vilket meddelande du vill att mottagaren ska se i brödtexten. För att testa fyller du i din egen e-postadress som mottagare.

   Klicka på **Lägg till dynamiskt innehåll** för att visa innehåll från meddelandet som du kan inkludera. Välj **Innehåll** – det inkluderar meddelandet i e-postmeddelandet. 

   ![Skärmbild som visar e-postalternativen för logikappen.](./media/tutorial-routing/logic-app-send-email.png)

9. Klicka på **Spara**. Logikappdesignern öppnas.

## <a name="set-up-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics

Om du vill se data i Power BI-visualiseringen konfigurerar du först ett Stream Analytics-jobb för att hämta data. Kom ihåg att endast meddelanden där **nivå** är satt till **normal** skickas till standardslutpunkten och hämtas av Stream Analytics-jobbet för Power BI-visualiseringen.

### <a name="create-the-stream-analytics-job"></a>Skapa Stream Analytics-jobbet

1. I [Azure-portalen](https://portal.azure.com) klickar du på **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Namnet på jobbet. Namnet måste vara globalt unikt. I självstudien används **contosoJob**.

   **Resursgrupp**: Använd samma resursgrupp som användes av IoT-hubben. I den här självstudien används **ContosoResources**. 

   **Plats**: Använd samma plats som användes i installationsskriptet. I den här självstudien används **USA, västra**. 

   ![Skärmbild som visar hur du skapar Stream Analytics-jobbet.](./media/tutorial-routing/stream-analytics-create-job.png)

3. Klicka på **Skapa** för att skapa jobbet. Om du vill gå tillbaka till jobbet klickar du på **Resursgrupper**. I den här självstudien används **ContosoResources**. Markera resursgruppen och klicka sedan på Stream Analytics-jobbet i listan över resurser. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

4. Under **Jobbtopologi** klickar du på **Indata**.

5. I rutan **Indata** klickar du på **Lägg till strömindata** och väljer IoT Hub. På skärmen som visas fyller du i följande fält:

   **Indataalias**: I självstudien används **contosoinputs**.

   **Prenumeration**: Välj din prenumeration.

   **IoT Hub**: Välj IoT Hub. I självstudien används **ContosoTestHub**.

   **Slutpunkt**: Välj **Meddelanden**. (Om du väljer Åtgärdsövervakning får du telemetridata om IoT-hubben istället för de data du skickar.) 

   **Namn på princip för delad åtkomst**: Välj **iothubowner**. Portalen fyller i Nyckel för princip för delad åtkomst åt dig.

   **Konsumentgrupp**: Välj konsumentgruppen du skapade tidigare. I den här självstudien används **contosoconsumers**.
   
   För resten av fälten accepterar du standardvärdena. 

   ![Skärmbild som visar hur du konfigurerar indata för Stream Analytics-jobbet.](./media/tutorial-routing/stream-analytics-job-inputs.png)

6. Klicka på **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Utdata**.

2. I fönstret **Utdata** klickar du på **Lägg till** och väljer **Power BI**. På skärmen som visas fyller du i följande fält:

   **Utdataalias**: Utdatas unika alias. I självstudien används **contosooutputs**. 

   **Namn på datauppsättning**: Namnet på datauppsättningen som ska användas i Power BI. I självstudien används **contosodataset**. 

   **Tabellnamn**: Namnet på tabellen som ska användas i Power BI. I självstudien används **contosotable**.

   Acceptera standardvärdena för resten av fälten.

3. Klicka på **Autentisera** och logga in på Power BI-kontot.

   ![Skärmbild som visar hur du konfigurerar utdata för Stream Analytics-jobbet.](./media/tutorial-routing/stream-analytics-job-outputs.png)

4. Klicka på **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Under **Jobbtopologi** klickar du på **Fråga**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet. I självstudien används **contosoinputs**.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet. I självstudien används **contosooutputs**.

   ![Skärmbild som visar hur du konfigurerar frågan för Stream Analytics-jobbet.](./media/tutorial-routing/stream-analytics-job-query.png)

4. Klicka på **Spara**.

5. Stäng rutan Fråga. Nu kommer du tillbaka till vyn över resurser i resursgruppen. Klicka på Stream Analytics-jobbet. I den här självstudien kallas det **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobbet klickar du på **Starta** > **Nu** > **Starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

För att konfigurera Power BI-rapporten behöver du data, så du måste konfigurera Power BI när du har skapat enheten och kör enhetssimuleringsprogrammet.

## <a name="run-simulated-device-app"></a>Köra en simulerad enhetsapp

Tidigare i avsnittet om skriptkonfiguration konfigurerade du en enhet för att använda en IoT-enhet. I det här avsnittet ska du ladda ned en .NET-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT Hub. Programmet skickar meddelanden för varje routningsmetod. 

Ladda ned lösningen för [IoT-enhetssimuleringen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Då laddas en lagringsplats som innehåller flera program ned. Lösningen du söker finns i iot-hub/Tutorials/Routing/SimulatedDevice/.

Dubbelklicka på lösningsfilen (SimulatedDevice.sln) för att öppna koden i Visual Studio och öppna sedan Program.cs. Ersätt `{iot hub hostname}` med IoT-hubbens värdnamn. Formatet för IoT-hubbens värdnamn är **{iot-hub-name}.azure-devices.net**. För den här självstudien är hubbens värdnamn **ContosoTestHub.azure-devices.net**. Ersätt därefter `{device key}` med enhetsnyckeln du sparade tidigare när du konfigurerade den simulerade enheten. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Kör och testa 

Kör konsolprogrammet. Vänta några minuter. Du kan se meddelandena som skickas på konsolskärmen i programmet.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Slumpmässigt tilldelas en nivå på `critical` eller `storage`, vilket orsakar att meddelandet dirigeras till lagringskontot eller till Service Bus-kön (som utlöser att logikappen skickar ett e-postmeddelande). Standardavläsningarna (`normal`) ska visas i BI-rapporten som du ställer in sedan.

Om allt är korrekt konfigurerat bör då se följande resultat:

1. Du börjar få e-postmeddelanden om kritiska meddelanden. 

   ![Skärmbild som visar resulterande e-postmeddelanden.](./media/tutorial-routing/results-in-email.png)

   Detta innebär följande:

   * Routningen till Service Bus-kön fungerar korrekt.
   * Logikappen som hämtar meddelandet från Service Bus-kön fungerar som den ska.
   * Logic App-anslutningsprogrammet till Outlook fungerar som det ska. 

2. I [Azure-portalen](https://portal.azure.com) klickar du på **Resursgrupper** och väljer resursgruppen. I den här självstudien används **ContosoResources**. Välj lagringskontot, klicka på **Blobar** och välj sedan containern. I självstudien används **contosoresults**. Du bör se en mapp, och du kan öka detaljnivån mellan kataloger tills du ser en eller flera filer. Öppna en av dessa filer. De innehåller poster som dirigeras till lagringskontot. 

   ![Skärmbild som visar resultatfilerna i lagringen.](./media/tutorial-routing/results-in-storage.png)

Detta innebär följande:

   * Routningen till lagringskontot fungerar korrekt.

Under tiden programmet fortfarande körs konfigurerar du Power BI-visualiseringen för att se om meddelandena skickas genom standardroutningen. 

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurera Power BI-visualiseringarna

1. Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto.

2. Gå till **Arbetsytor** och välj arbetsytan som du angav när du skapade utdata för Stream Analytics-jobbet. Den här självstudien använder **Min arbetsyta**. 

3. Klicka på **Datauppsättningar**.

   Du bör se den listade datauppsättningen som du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosodataset**. (Det kan ta 5–10 minuter innan datauppsättningen visas första gången.)

4. Under **ÅTGÄRDER** klickar du på den första ikonen för att skapa en rapport.

   ![Skärmbild som visar Power BI-arbetsytan med ikonen för åtgärder och rapporter markerad.](./media/tutorial-routing/power-bi-actions.png)

5. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   * På sidan för skapande av rapport lägger du till ett linjediagram genom att klicka på linjediagramikonen.

   ![Skärmbild som visar visualiseringar och fält.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   * I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosotable**.

   * Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   * Dra **temperatur** till **Värden**.

   Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

6. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. För att konfigurera ett andra diagram följer du samma steg som ovan och placerar **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Skärmbild som visar den slutgiltiga Power BI-rapporten med två diagram.](./media/tutorial-routing/power-bi-report.png)

7. Klicka på **Spara** för att spara rapporten.

Du bör kunna se data i båda diagrammen. Detta innebär följande:

   * Routningen till standardslutpunkten fungerar korrekt.
   * Azure Stream Analytics-jobbet strömmar som det ska.
   * Power BI-visualiseringen är korrekt konfigurerad.

Du kan uppdatera diagrammen för att se senaste data genom att klicka på uppdateringsknappen högst upp i Power BI-fönstret. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du vill ta bort alla resurser du har skapat tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar det bort IoT-hubben, Service Bus-namnrymden och kön, logikappen, lagringskontot och själva resursgruppen. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Rensa resurser i Power BI-visualiseringen

Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto. Gå till din arbetsyta. Den här självstudien använder **Min arbetsyta**. Om du vill ta bort Power BI-visualiseringen går du till DataSets, klickar på papperskorgsikonen och tar bort datauppsättningen. I självstudien används **contosodataset**. När du tar bort datauppsättningen tas även rapporten bort.

### <a name="clean-up-resources-using-azure-cli"></a>Rensa resurser med hjälp av Azure CLI

Om du vill ta bort resursgruppen använder du kommandot [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Rensa resurser med hjälp av PowerShell

Om du vill ta bort resursgruppen använder du kommandot [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup). $resourceGroup ställdes in på **ContosoIoTRG1** i början av den här självstudien.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda meddelanderoutning för att dirigera IoT Hub-meddelanden till olika mål genom att utföra följande uppgifter.  

> [!div class="checklist"]
> * Konfigurera grundläggande resurser med Azure CLI eller PowerShell – en IoT-hubb, ett lagringskonto, en Service Bus-kö och en simulerad enhet.
> * Konfigurera slutpunkter och vägar i IoT Hub för lagringskontot och Service Bus-kön.
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
[Konfigurera och använda mått och diagnostik med en IoT-hubb](tutorial-use-metrics-and-diags.md)
