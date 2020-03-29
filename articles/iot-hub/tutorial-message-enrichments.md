---
title: Självstudiekurs - Använda Azure IoT Hub-meddelandeberikade
description: Självstudiekurs som visar hur du använder meddelandeberikanden för Azure IoT Hub-meddelanden
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674359"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Självstudiekurs: Använda Azure IoT Hub-meddelandeberikade

*Meddelandeberikningar* beskriver möjligheten för Azure IoT Hub att *stämpla* meddelanden med ytterligare information innan meddelandena skickas till den angivna slutpunkten. En anledning till att använda meddelandeberikande är att inkludera data som kan användas för att förenkla nedströmsbearbetning. Om du till exempel kan berika telekommunikationsmeddelanden för enheter med en enhetstvillingtagg minska belastningen på kunder för att ringa enhetstvilling-API-anrop för den här informationen. Mer information finns i [Översikt över meddelandeberikande .](iot-hub-message-enrichments-overview.md)

I den här självstudien visas två sätt att skapa och konfigurera de resurser som behövs för att testa meddelandeberikade för en IoT-hubb. Resurserna innehåller ett lagringskonto med två lagringsbehållare. En behållare innehåller de utökade meddelandena och en annan behållare innehåller de ursprungliga meddelandena. Dessutom ingår en IoT-hubb för att ta emot meddelanden och dirigera dem till lämplig lagringsbehållare baserat på om de är berikade eller inte.

* Den första metoden är att använda Azure CLI för att skapa resurser och konfigurera meddelanderoutning. Sedan definierar du anrikningarna manuellt med hjälp av [Azure-portalen](https://portal.azure.com).

* Den andra metoden är att använda en Azure Resource Manager-mall för att skapa både resurser *och* konfigurationer för meddelanderoutning och meddelandeberikanden.

När konfigurationerna för meddelanderoutning och meddelandeberikande är klara använder du ett program för att skicka meddelanden till IoT-hubben. Navet dirigerar dem sedan till båda lagringsbehållarna. Endast de meddelanden som skickas till slutpunkten för den **berikade lagringsbehållaren** berikas.

Här är de uppgifter du utför för att slutföra den här självstudien:

**Använda IoT Hub-meddelandeberikade**
> [!div class="checklist"]
> * Första metoden: Skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelandeberikade manuellt med hjälp av [Azure-portalen](https://portal.azure.com).
> * Andra metoden: Skapa resurser och konfigurera meddelanderoutning och meddelandeberikade med hjälp av en Resource Manager-mall. 
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till navet.
> * Visa resultaten och kontrollera att meddelandeberikade fungerar som förväntat.

## <a name="prerequisites"></a>Krav

* Du måste ha en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.
* Installera [Visual Studio](https://www.visualstudio.com/).

* Kontrollera att port 8883 är öppen i brandväggen. Enhetsexemplet i den här självstudien använder MQTT-protokollet, som kommunicerar över port 8883. Den här porten kan vara blockerad i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att lösa problemet finns i [Ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Hämta databasen IoT C#-exempel

Ladda ner [IoT C#-exemplen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) från GitHub och packa upp dem. Den här databasen har flera program, skript och Resource Manager-mallar i den. De som ska användas för den här guiden är följande:

* För den manuella metoden finns det ett CLI-skript som används för att skapa resurserna. Det här skriptet finns i /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Det här skriptet skapar resurserna och konfigurerar meddelanderoutningen. När du har kört skriptet skapar du meddelandeberikade manuellt med hjälp av [Azure-portalen](https://portal.azure.com).
* För den automatiska metoden finns en Azure Resource Manager-mall. Mallen finns i /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Den här mallen skapar resurserna, konfigurerar meddelanderoutningen och konfigurerar sedan meddelandeberikade.
* Det tredje programmet du använder är appen Enhetssimulering, som du använder för att skicka meddelanden till IoT-hubben och testa meddelandeberikande.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Konfigurera och konfigurera manuellt med hjälp av Azure CLI

Förutom att skapa nödvändiga resurser konfigurerar Azure CLI-skriptet också de två vägarna till slutpunkterna som är separata lagringsbehållare. Mer information om hur du konfigurerar meddelanderoutningen finns i [självstudiekursen För routning](tutorial-routing.md). När resurserna har konfigurerats använder du [Azure-portalen](https://portal.azure.com) för att konfigurera meddelandeberikande för varje slutpunkt. Fortsätt sedan till teststeget.

> [!NOTE]
> Alla meddelanden dirigeras till båda slutpunkterna, men endast de meddelanden som går till slutpunkten med konfigurerade meddelandeberikningar kommer att berikas.
>

Du kan använda skriptet som följer eller öppna skriptet i mappen /resources i den hämtade databasen. Skriptet utför följande steg:

* Skapa en IoT-hubb.
* Skapa ett lagringskonto.
* Skapa två behållare i lagringskontot. En behållare är till för de utökade meddelandena och en annan behållare är för meddelanden som inte är berikade.
* Ställ in routning för de två olika lagringskontona:
    * Skapa en slutpunkt för varje lagringskontobehållare.
    * Skapa en väg till var och en av slutpunkterna för lagringskontobehållaren.

Det finns flera resursnamn som måste vara globalt unika, till exempel IoT-hubbens namn och lagringskontonamnet. För att göra det enklare att köra skriptet läggs dessa resursnamn till med ett slumpmässigt alfanumeriskt värde som kallas *randomValue*. Det slumpmässiga värdet genereras en gång högst upp i skriptet. Det läggs till i resursnamnen efter behov i hela skriptet. Om du inte vill att värdet ska vara slumpmässigt kan du ställa in det på en tom sträng eller till ett visst värde.

Om du inte redan har gjort det öppnar du ett Azure [Cloud Shell-fönster](https://shell.azure.com) och ser till att det är inställt på Bash. Öppna skriptet i den uppackade databasen, välj Ctrl+A för att markera allt och välj sedan Ctrl+C för att kopiera det. Alternativt kan du kopiera följande CLI-skript eller öppna det direkt i Cloud Shell. Klistra in skriptet i fönstret Cloud Shell genom att högerklicka på kommandoraden och välja **Klistra in**. Skriptet körs en sats i taget. När skriptet har slutat köras väljer du **Retur** för att kontrollera att det körs det sista kommandot. Följande kodblock visar skriptet som används, med kommentarer som förklarar vad det gör.

Här är de resurser som skapats av skriptet. *Berikad* innebär att resursen är för meddelanden med berikande. *Original* innebär att resursen är för meddelanden som inte är berikade.

| Namn | Värde |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| behållarnamn | Ursprungliga  |
| behållarnamn | Berikad  |
| IoT-enhetsnamn | Contoso-Test-Enhet |
| IoT Hub-namn | ContosoTestHubMsgEn |
| namn på lagringskonto | contosostorage (contosostorage) |
| slutpunktsnamn 1 | ContosoStorageEndpointOriginal |
| slutpunktsnamn 2 | ContosoStorageEndpointRiched|
| ruttnamn 1 | ContosoStorageRouteOriginal |
| ruttnamn 2 | ContosoStorageRouteRiched |

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
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
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
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Nu är resurserna alla inställda och meddelanderoutningen har konfigurerats. Du kan visa konfigurationen för meddelanderoutning i portalen och ställa in meddelandeberikade meddelanden för meddelanden som går till den **berikade lagringsbehållaren.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Konfigurera meddelandeberikade manuellt med hjälp av Azure-portalen

1. Gå till IoT-hubben genom att välja **Resursgrupper**. Välj sedan den resursgrupp som ställts in för den här självstudien (**ContosoResourcesMsgEn**). Leta reda på IoT-hubben i listan och välj den. Välj **Meddelanderoutning** för IoT-hubben.

   ![Välj meddelanderoutning](./media/tutorial-message-enrichments/select-iot-hub.png)

   Meddelanderoutningsfönstret har tre flikar med **etiketterna Vägar,** **Anpassade slutpunkter**och **Utökade meddelanden**. Bläddra bland de två första flikarna för att se den konfiguration som skriptet har konfigurerat. Använd den tredje fliken för att lägga till meddelandeberikade. Låt oss berika meddelanden som går till slutpunkten för lagringsbehållaren som kallas **berikad**. Fyll i namnet och värdet och välj sedan slutpunkten **ContosoStorageEndpointEnriched** i listrutan. Här är ett exempel på hur du konfigurerar en anrikning som lägger till IoT-hubbnamnet i meddelandet:

   ![Lägg till första anrikning](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Lägg till dessa värden i listan för slutpunkten ContosoStorageEndpointEnriched.

   | Nyckel | Värde | Slutpunkt (listruta) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Enhetslokalisering | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Om enheten inte har en tvilling stämplas värdet du lägger in här som en sträng för värdet i meddelandeberikningarna. Om du vill se enhetstvillinginformationen går du till navet i portalen och väljer **IoT-enheter**. Välj enheten och välj sedan **Enhetstvilling** högst upp på sidan.
   >
   > Du kan redigera den dubbla informationen för att lägga till taggar, till exempel plats, och ange den till ett visst värde. Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

3. När du är klar ska fönstret se ut ungefär som den här bilden:

   ![Tabell med alla anrikningar tillagda](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Välj **Använd** om du vill spara ändringarna. Gå till avsnittet [Testmeddelandeberikande.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Skapa och konfigurera med hjälp av en Resource Manager-mall
Du kan använda en Resource Manager-mall för att skapa och konfigurera resurser, meddelanderoutning och meddelandeberikande.

1. Logga in på Azure Portal. Välj **+ Skapa en resurs** för att visa en sökruta. Ange *malldistribution*och sök efter den. I resultatfönstret väljer du **Malldistribution (distribuera med anpassad mall)**.

   ![Malldistribution i Azure-portalen](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Välj **Skapa** i **distributionsfönstret för mall.**

1. I fönstret **Anpassad distribution** väljer du Skapa en egen mall **i redigeraren**.

1. Välj **Läs in fil**i fönstret **Redigera** mall . Utforskaren visas. Leta upp **filen template_messageenrichments.json** i den uppackade repo-filen i **/iot-hub/Tutorials/Routing/SimulatedDevice/resources**. 

   ![Välj mall från lokal dator](./media/tutorial-message-enrichments/template-select.png)

1. Välj **Öppna** om du vill läsa in mallfilen från den lokala datorn. Den läses in och visas i redigeringsfönstret.

   Den här mallen är inställd på att använda ett globalt unikt IoT-hubbnamn och lagringskontonamn genom att lägga till ett slumpmässigt värde i slutet av standardnamnen, så att du kan använda mallen utan att göra några ändringar i den.

   Här är de resurser som skapas genom att läsa in mallen. **Berikad** innebär att resursen är för meddelanden med berikande. **Original** innebär att resursen är för meddelanden som inte är berikade. Det här är samma värden som används i Azure CLI-skriptet.

   | Namn | Värde |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | behållarnamn | Ursprungliga  |
   | behållarnamn | Berikad  |
   | IoT-enhetsnamn | Contoso-Test-Enhet |
   | IoT Hub-namn | ContosoTestHubMsgEn |
   | namn på lagringskonto | contosostorage (contosostorage) |
   | slutpunktsnamn 1 | ContosoStorageEndpointOriginal |
   | slutpunktsnamn 2 | ContosoStorageEndpointRiched|
   | ruttnamn 1 | ContosoStorageRouteOriginal |
   | ruttnamn 2 | ContosoStorageRouteRiched |

1. Välj **Spara**. Fönstret **Anpassad distribution** visas och visar alla parametrar som används av mallen. Det enda fält som du behöver ange är **resursgruppen**. Skapa en ny eller välj en i listrutan.

   Här är den övre halvan av fönstret **Anpassad distribution.** Du kan se var du fyller i resursgruppen.

   ![Övre halvan av fönstret Anpassad distribution](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Här är den nedre halvan av fönstret **Anpassad distribution.** Du kan se resten av parametrarna och villkoren. 

   ![Nedre halvan av fönstret Anpassad distribution](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Markera kryssrutan om du vill godkänna villkoren. Välj sedan **Inköp** för att fortsätta med malldistributionen.

1. Vänta tills mallen har distribuerats helt. Välj klockikonen högst upp på skärmen för att kontrollera förloppet. När den är klar fortsätter du till avsnittet [Testmeddelandeberikande.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Berikningar om testmeddelanden

Om du vill visa meddelandeberikningarna väljer du **Resursgrupper**. Välj sedan den resursgrupp som du använder för den här självstudien. Välj IoT-hubben i listan över resurser och gå till **Meddelanden**. Konfigurationen för meddelanderoutning och de konfigurerade anrikningsorden visas.

Nu när meddelandeberikningarna har konfigurerats för slutpunkten kör du programmet Simulerad enhet för att skicka meddelanden till IoT-hubben. Navet har konfigurerats med inställningar som utför följande uppgifter:

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointOriginal kommer inte att berikas `original`och lagras i lagringsbehållaren .

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointEnriched kommer att `enriched`berikas och lagras i lagringsbehållaren .

Programmet Simulerad enhet är ett av programmen i den uppackade nedladdningen. Programmet skickar meddelanden för var och en av de olika meddelanderoutningsmetoderna i [självstudiekursen Routning](tutorial-routing.md), som innehåller Azure Storage.

Dubbelklicka på lösningsfilen **IoT_SimulatedDevice.sln** för att öppna koden i Visual Studio och öppna **sedan Program.cs**. Ersätt IoT-hubbnamnet `{your hub name}`för markören . Formatet för värdnamnet för IoT-hubb är **{ditt navnamn}.azure-devices.net**. För den här självstudien är navvärdens namn ContosoTestHubMsgEn.azure-devices.net. Ersätt sedan enhetsnyckeln som du sparade tidigare när du `{your device key}`körde skriptet för att skapa resurserna för markören .

Om du inte har enhetsnyckeln kan du hämta den från portalen. När du har loggat in går du till **Resursgrupper,** väljer resursgruppen och väljer sedan din IoT-hubb. Titta under **IoT-enheter** efter testenheten och välj din enhet. Markera kopieringsikonen **bredvid Primär tangenten** för att kopiera den till Urklipp.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Kör och testa

Kör konsolprogrammet i några minuter. De meddelanden som skickas visas på konsolens skärm i programmet.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Det tilldelar slumpmässigt `critical` en `storage`nivå eller , vilket gör att meddelandet dirigeras till lagringskontot eller till standardslutpunkten. Meddelandena som skickas till den **utökade** behållaren i lagringskontot kommer att berikas.

När flera lagringsmeddelanden har skickats visar du data.

1. Välj **Resursgrupper**. Leta reda på resursgruppen **ContosoResourcesMsgEn**och markera den.

2. Välj ditt lagringskonto, som är **contosostorage**. Välj sedan **Storage Explorer (förhandsgranskning)** i den vänstra rutan.

   ![Välj Lagringsutforskaren](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Välj **BLOB CONTAINERS om** du vill visa de två behållare som kan användas.

   ![Se behållarna i lagringskontot](./media/tutorial-message-enrichments/show-blob-containers.png)

Meddelandena i behållaren som kallas **berikad** har meddelandet berikande inkluderat i meddelandena. Meddelandena i behållaren som kallas **original** har råa meddelanden utan anrikning. Öka detaljnivån i en av behållarna tills du kommer längst ned och öppna den senaste meddelandefilen. Gör sedan samma sak för den andra behållaren för att kontrollera att det inte finns några enrichments som läggs till i meddelanden i den behållaren.

När du tittar på meddelanden som har berikats bör du se "min IoT Hub" med hubbens namn och plats och kund-ID, så här:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Här är ett oenriktat meddelande. Observera att "min IoT Hub", "devicelocation" och "customerID" inte visas här eftersom dessa fält läggs till av berikande. Den här slutpunkten har inga anrikningar.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du har skapat i den här självstudien tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort IoT-hubben, lagringskontot och själva resursgruppen.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Använd Azure CLI för att rensa resurser

Om du vill ta bort resursgruppen använder du kommandot [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Minns `$resourceGroup` att var inställd på **ContosoResourcesMsgEn** i början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du konfigurerat och testat att lägga till meddelandeberikade i IoT Hub-meddelanden med hjälp av följande steg:

**Använda IoT Hub-meddelandeberikade**
> [!div class="checklist"]
> * Första metoden: Skapa resurser och konfigurera meddelanderoutning med hjälp av Azure CLI. Konfigurera meddelandeberikade manuellt med hjälp av [Azure-portalen](https://portal.azure.com).
> * Andra metoden: Skapa resurser och konfigurera meddelanderoutning och meddelandeberikade med hjälp av en Azure Resource Manager-mall.
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till navet.
> * Visa resultaten och kontrollera att meddelandeberikade fungerar som förväntat.

Mer information om meddelandeberikande finns i [Översikt över meddelandeberikande .](iot-hub-message-enrichments-overview.md)

Mer information om meddelanderoutning finns i följande artiklar:

* [Använda IoT Hub-meddelanderoutning för att skicka meddelanden från enhet till moln till olika slutpunkter](iot-hub-devguide-messages-d2c.md)
* [Självstudiekurs: IoT Hub-routning](tutorial-routing.md)
