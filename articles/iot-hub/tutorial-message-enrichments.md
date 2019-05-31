---
title: Självstudier – använda Azure IoT Hub meddelande enrichments
description: Självstudie som visar hur du använder meddelande enrichments för Azure IoT Hub-meddelanden
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259081"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Självstudier: Med hjälp av Azure IoT Hub meddelandet enrichments (förhandsversion)

*Meddelandet enrichments* möjligt för IoT-hubben till *stämpel* meddelanden med ytterligare information innan meddelandena skickas till den angivna slutpunkten. Ett skäl till att använda meddelande enrichments är att ta med data som kan användas för att förenkla bearbetningen nedströms. Till exempel kan berikas enhetsmeddelanden telemetri med en twin enhetstagg minska belastningen på kunder för att se enhetstvillingen API-anrop för den här informationen. Mer information finns i den [översikt över meddelande enrichments](iot-hub-message-enrichments-overview.md).

I den här självstudien får du använda Azure CLI för att ställa in resurser, inklusive två slutpunkter som pekar på två olika storage-behållare – **berikats** och **ursprungliga**. Kan du använda den [Azure-portalen](https://portal.azure.com) att konfigurera meddelandet enrichments tillämpas endast på meddelanden som skickas till slutpunkten med den **berikats** lagringsbehållare. Du skicka meddelanden till IoT-hubb som dirigeras till båda storage-behållare. Endast meddelanden som skickats till slutpunkten för den **berikats** lagringsbehållare som ska utökas.

Här följer de uppgifter som du utför den här kursen:

**Med hjälp av IoT Hub meddelande enrichments**
> [!div class="checklist"]
> * Med Azure CLI kan skapa resurser – en IoT-hubb och ett lagringskonto med två slutpunkter routningskonfigurationen.
> * Använd Azure-portalen för att konfigurera meddelandet enrichments.
> * Kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben.
> * Granska resultaten och kontrollera meddelandet enrichments fungerar som förväntat.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Du måste ha en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Installera [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Hämta exempelkoden

Ladda ned [IoT Enhetssimulering](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) och packa upp den. Den här lagringsplatsen innehåller flera program, inklusive den som du använder för att skicka meddelanden till IoT hub.

Den här filen innehåller också skript för att skapa de resurser som används för att testa meddelande enrichments. Skriptet finns på /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Du kan titta på skriptet och använda den för tillfället. Du kan också kopiera skriptet direkt från artikeln.

När du är redo att börja testa, använder du Enhetssimulering programmet från den här nedladdningen för att skicka meddelande till din IoT-hubb.

## <a name="set-up-and-configure-resources"></a>Installera och konfigurera resurser

Azure CLI-skriptet konfigurerar också två vägar till de slutpunkter som är separat lagringsbehållare förutom att skapa resurserna som krävs. Mer information om hur du konfigurerar Routning finns i den [routning självstudien](tutorial-routing.md). När resurserna har konfigurerats kan du använda den [Azure-portalen](https://portal.azure.com) att konfigurera meddelandet enrichments för varje slutpunkt och fortsätt sedan till testning steg.

> [!NOTE]
> Alla meddelanden dirigeras till båda slutpunkter, men endast meddelanden som skickas till slutpunkt med konfigurerade meddelande enrichments ska utökas.
>

Du kan använda skriptet nedan eller öppna skriptet i mappen /resources av hämtade lagringsplatsen. Här är de steg som skriptet ska utföra:

* Skapa en IoT Hub.
* Skapa ett lagringskonto.
* Skapa två behållare i storage-konto – en för avancerad och meddelanden och en för meddelanden som inte har utökat.
* Konfigurera routning för två olika lagringskonton.
    * Skapa en slutpunkt för varje lagringskontobehållare.
    * Skapa en väg till var och en av de behållaren slutpunkterna för lagringskonto.

Det finns flera resursnamn som måste vara globalt unikt, till exempel IoT Hub-namn och namnet på lagringskontot. För att göra som kör skriptet, läggs de resursnamn med slumpmässiga alfanumeriska värdet kallas *randomValue*. RandomValue skapas en gång överst i skriptet och läggas till för resursnamnen efter behov i skriptet. Om du inte vill att det ska vara slumpmässiga kan konfigurera du den till en tom sträng eller till ett specifikt värde.

Om du inte redan har gjort det öppnar en [Cloud Shell-fönstret för Bash.](https://shell.azure.com). Öppna skriptet i uppzippade databasen, Använd Ctrl-A för att markera dem och sedan Ctrl + C. Du kan också kopiera följande CLI-skript eller öppna den direkt i cloudshell. Klistra in skriptet i Azure cloud shell-fönstret genom att högerklicka på kommandoraden och välja **klistra in**. Skriptet körs en instruktion i taget. När skriptet har körts, väljer **RETUR** att kontrollera att den körs det sista kommandot. Följande kodblock visar det skript som används med kommentarer som förklarar vad det gör.

Här är de resurser som skapats av skriptet. **Utökad** innebär att resursen är för meddelanden med enrichments. **Ursprungliga** innebär att resursen är för meddelanden som inte har utökat.

| Namn | Värde |
|-----|-----|
| ResourceGroup | ContosoResourcesMsgEn |
| Behållarnamn | original  |
| Behållarnamn | utökad  |
| Namnet på IoT-enhet | Contoso-Test-Device |
| IoT Hub-namn | ContosoTestHubMsgEn |
| Lagringskontonamn | contosostorage |
| slutpunkt 1 namn | ContosoStorageEndpointOriginal |
| slutpunkt namn 2 | ContosoStorageEndpointEnriched|
| dirigera namn 1 | ContosoStorageRouteOriginal |
| dirigera namn 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
subscriptionID=$(az account show --query id -o tsv)

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
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

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

# This is the endpoint for container 1, for endpoint messages that are not enriched.
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

# This is the endpoint for container 2, for endpoint messages that are enriched.
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

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

Nu resurserna som är klart och routning är konfigurerad. Du kan visa meddelandet routningskonfiguration i portalen och ställa in meddelande enrichments för meddelanden till den **berikats** lagringsbehållare.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Visa Routning och konfigurera meddelande enrichments

1. Gå till din IoT-hubb genom att välja **resursgrupper**, välj sedan den resursgrupp som ställts in för den här självstudien (**ContosoResources_MsgEn**). Hitta IoT-hubben i listan och välj den. Välj *meddelanderoutning** för Iot-hubben.

   ![Välj meddelanderoutning](./media/tutorial-message-enrichments/select-iot-hub.png)

   Meddelandet routning fönstret har tre flikar-- **vägar**, **anpassade slutpunkter**, och **berikar meddelanden**. Du kan bläddra först två flikar om du vill se konfigurationen som konfigurerats av skriptet. Använd den tredje fliken för att lägga till meddelandet enrichments. Nu ska vi berikar meddelanden som skickas till slutpunkten för behållaren heter **berikats**. Fyll i namnet och värdet och sedan väljer du slutpunkten **ContosoStorageEndpointEnriched** från den nedrullningsbara listan. Här är ett exempel på hur du konfigurerar en berikande som lägger till IoT Hub-namn i meddelandet:

   ![Lägg till första berikande](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Lägg till dessa värden i listan för ContosoStorageEndpointEnriched-slutpunkten.

   | Namn | Värde | Slutpunkt (listruta) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Enhetsplats | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |CustomerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Om enheten inte har en enhetstvilling, kommer det värdet som du placerar i här stämplas som en sträng för värdet i meddelandet enrichments. Välj sedan enheten twin information, gå till din hubb på portalen visas **IoT-enheter**, Välj din enhet och välj sedan **enhetstvillingen** överst på sidan.
   >
   > Du kan redigera enhetstvilling-information för att lägga till taggar (till exempel plats) och ange den till ett specifikt värde om du vill. Mer information finns i [Understand and use device twins in IoT Hub](iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub)

3. När du är klar, bör fönstret likna den här bilden:

   ![Tabell med alla enrichments har lagts till](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Välj **tillämpa** att spara ändringarna.

## <a name="send-messages-to-the-iot-hub"></a>Skicka meddelanden till IoT Hub

Nu när meddelandet enrichments har konfigurerats för slutpunkten, kör Simulated Device-programmet för att skicka meddelanden till IoT Hub. Hubben har konfigurerats med regler som utför följande:

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointOriginal kommer inte utökas och kommer att lagras i lagringsbehållaren `original`.

* Meddelanden som dirigeras till lagringsslutpunkten ContosoStorageEndpointEnriched kommer berikats och lagras i lagringsbehållaren `enriched`.

Simulated Device-program är ett program i uppzippade nedladdningen. Programmet skickar meddelanden för varje routningsmetoder för annat meddelande i den [routning självstudien](tutorial-routing.md); Detta omfattar Azure Storage.

Dubbelklicka på lösningsfilen (IoT_SimulatedDevice.sln) för att öppna koden i Visual Studio och öppna Program.cs. Ersätt `{your hub name}` med namnet för IoT-hubb. Formatet för IoT hub-värdnamnet är **{hub name} .azure devices.net**. Den här självstudien hub-värdnamnet är **ContosoTestHubMsgEn.azure devices.net**. Ersätt sedan `{device key}` med enhetsnyckeln som du sparade tidigare när du kör skriptet för att skapa resurser.

Om du inte har enhetsnyckeln, kan du hämta den från portalen. När du loggar in, gå till **resursgrupper**, Välj din resursgrupp och välj sedan din IoT-hubb. Titta under **IoT-enheter** för testenhet och välj din enhet. Välj Kopiera-ikonen bredvid **primärnyckel** att kopiera den till Urklipp.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Kör och testa

Kör konsolprogrammet. Vänta några minuter. De meddelanden som skickas visas i konsolen för programmet.

Appen skickar ett nytt enhet till molnet-meddelande till IoT-hubben varje sekund. Meddelandet innehåller ett JSON-serialiserat objekt med enhets-ID, temperatur, luftfuktighet och meddelandenivå, där standardinställningen är `normal`. Slumpmässigt tilldelar en nivå av `critical` eller `storage`, orsakar meddelandet ska dirigeras till lagringskontot eller till standardslutpunkt. Meddelanden som skickats till den **berikats** behållare i lagringskontot som ska utökas.

Visa data efter flera storage meddelanden har skickats.

1. Välj **resursgrupper**, leta upp din resursgrupp (ContosoResourcesMsgEn) och markera den.

2. Välj ditt lagringskonto (contosostorage). Välj sedan **Lagringsutforskaren (förhandsversion)** från Markeringsfönstret till vänster.

   ![Välj Lagringsutforskaren](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Välj **BLOBBEHÅLLARE** att se de två behållare som kan användas.

   ![Se behållarna i lagringskontot](./media/tutorial-message-enrichments/show-blob-containers.png)

Meddelandena i behållaren heter **berikats** har meddelande-enrichments som ingår i meddelanden. Meddelandena i behållaren heter **ursprungliga** har rådata meddelanden med inga enrichments. Granska nedåt i någon av behållarna tills du kommer längst ned och öppna meddelandefilen senaste och gör samma sak för behållaren att verifiera att det finns inga enrichments som lagts till meddelanden i den behållaren.

När du tittar på meddelanden som har varit utökat, bör du se ”mina IoT-hubben” med hubbnamnet, samt platsen och kund-ID, så här:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Och här är en icke inredda. ”” Min IoT-hubb ”,” enhetsplats ”och” customerID ”visas inte här, eftersom den här slutpunkten har inga enrichments.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du har skapat i den här självstudien tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort IoT-hubben, lagringskontot och själva resursgruppen.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Använda Azure CLI för att rensa resurser

Om du vill ta bort resursgruppen använder du kommandot [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` var inställd **ContosoResources** tillbaka i början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har konfigurerats och testats att lägga till meddelandet enrichments att meddelanden från IoT Hub med hjälp av följande steg:

**Med hjälp av IoT Hub meddelande enrichments**
> [!div class="checklist"]
> * Med Azure CLI kan skapa resurser – en IoT-hubb och ett lagringskonto med två enendpoints routningskonfigurationen.
> * Använd Azure-portalen för att konfigurera meddelandet enrichments.
> * Kör en app simulerar som en IoT-enheter att skicka meddelandet till hubben.
> * Granska resultaten och kontrollera meddelandet enrichments fungerar som förväntat.

Mer information om meddelandet enrichments finns i den [översikt över meddelande enrichments](iot-hub-message-enrichments-overview.md).

Mer information om meddelanderoutning finns i följande artiklar:

* [Använd IoT Hub meddelanderoutning för att skicka enhet-till-moln-meddelanden till olika slutpunkter](iot-hub-devguide-messages-d2c.md)

* [Självstudie: IoT Hub-Routning](tutorial-routing.md)