---
title: Konfigurera och använda mått och diagnostikloggar med Azure IoT Hub | Microsoft Docs
description: Konfigurera och använda mått och diagnostikloggar med Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 8bcc72cf151b085c7f65b6c600a49642cd330bac
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54248494"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>Självstudier: Konfigurera och använda mått och diagnostikloggar med en IoT-hubb

Om du har en IoT Hub-lösning som körs i produktionsmiljön ska du konfigurera vissa mått och aktivera diagnostikloggar. Om ett problem uppstår har du sedan data att titta på som hjälper dig att diagnostisera problemet och åtgärda problemet snabbare. I den här artikeln ser du hur du aktiverar diagnostikloggarna och hur du kontrollerar dem efter fel. Du kan också ställa in några mått att titta på och aviseringar som utlöses när måtten når en viss gräns. Du kan till exempel ha ett e-postmeddelande som skickats till dig när antalet skickade telemetrimeddelanden överskrider en viss gräns, eller när antalet meddelanden som används närmar sig kvoten av meddelanden som tillåts per dag för IoT-hubben. 

En exempelanvändning är en bensinstation där pumpar är IoT-enheter som kommunicerar med en IoT-hubb. Kreditkort verifieras och den slutliga transaktionen skrivs till ett datalager. Om IoT-enheterna slutar ansluta till hubben och skicka meddelanden är det mycket svårare att lösa om du inte har någon insyn i vad händer.

I den här självstudien används Azure-exempel från [IoT Hub-dirigering](tutorial-routing.md) för att skicka meddelanden till IoT Hub.

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
> * Använda Azure C, skapa en IoT-hubb, en simulerad enhet och ett lagringskonto.  
> * Aktivera diagnostikloggar.
> * Aktivera mätvärden.
> * Konfigurera aviseringar för dessa mått. 
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben. 
> * Kör appen tills aviseringarna börjar utlösas. 
> * Visa måttresultaten och kontrollera diagnostikloggarna. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Installera [Visual Studio](https://www.visualstudio.com/). 

- Ett e-postkonto som kan ta emot e-post.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>Konfigurera resurser

För den är självstudien använder du en IoT-hubb, ett lagringskonto och en simulerad IoT-enhet. De här resurserna kan skapas med Azure CLI eller Azure PowerShell. Använd samma resursgrupp och plats för alla resurser. I slutet av självstudien kan du ta bort allt i ett steg genom att ta bort resursgruppen.

Det här är steg som krävs.

1. Skapa en [resursgrupp](../azure-resource-manager/resource-group-overview.md). 

2. Skapa en IoT-hubb.

3. Skapa ett V1-standardlagringskonto med Standard_LRS-replikering.

4. Skapa en enhetsidentitet för den simulerade enheten som skickar meddelanden till din hubb. Spara nyckeln för testfasen.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurera resurser med hjälp av Azure CLI

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad. De nya resurserna skapas i resursgruppen ContosoResources.

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
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>När du skapar enhetens identitet kan du få följande fel: *Inga nycklar hittades för principen iothubowner av IoT Hub ContosoTestHub*. Åtgärda felet genom att uppdatera IoT-tillägget för Azure CLI och kör de senaste två kommandona i skriptet igen. 
>
>Här är kommandot för att uppdatera tillägget. Kör det i Cloud Shell-instansen.
>
>```cli
>az extension update --name azure-cli-iot-ext
>```

## <a name="enable-the-diagnostic-logs"></a>Aktivera diagnostikloggarna 

[Diagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md) är inaktiverade som standard när du skapar en ny IoT-hubb. Aktivera diagnostikloggar för din hubb i det här avsnittet.

1. Om du inte redan är på hubben i portalen klickar du först på **Resursgrupper** och sedan på resursgruppen Contoso-Resources. Välj hubben från listan över resurser som visas. 

2. Leta upp **övervakningsavsnittet** på IoT Hub-bladet. Klicka på **Diagnostikinställningar**. 

   ![Skärmbild som visar diagnostikinställningar för en del av IoT Hub-bladet.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. Kontrollera att prenumerationen och resursgruppen är korrekta. Under **Resurstyp** avmarkerar du **Markera alla** och letar upp och markerar **IoT Hub**. (Detta placerar markeringen bredvid *Markera alla* igen, ignorera det.) Under **Resurs** väljer du hubbnamnet. Skärmen bör se ut som på bilden: 

   ![Skärmbild som visar diagnostikinställningar för en del av IoT Hub-bladet.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. Klicka nu på **Slå på diagnostik**. Fönstret Diagnostikinställningar visas. Ge inställningarna för diagnostikloggar namnet ”diags-hub”.

5. Markera **Arkivera till ett lagringskonto**. 

   ![Skärmbild som visar när diagnostiken ställs in för att arkivera till ett lagringskonto.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    Klicka på **Konfigurera** för att se skärmen **Select a storage account** (Välj ett lagringskonto), välj rätt (*contosostoragemon*) och klicka på **OK** för att gå tillbaka till fönstret med diagnostikinställningar. 

   ![Skärmbild som visar när diagnostikloggar ställs in för att arkivera till ett lagringskonto.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. Under **LOGG** markerar du **Anslutningar** och **Device Telemetry** (Enhetstelemetri) och ställer in **Kvarhållning (dagar)** på 7 dagar för var och en. Skärmen Diagnostikinställningar bör nu se ut som på bilden:

   ![Skärmbild som visar de slutliga diagnostiklogginställningarna.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. Spara inställningarna genom att klicka på **Spara**. Stäng fönstret Diagnostik.

När du tittar på diagnostikloggarna senare kommer du att kunna se loggning för anslutning och frånkoppling för enheten. 

## <a name="set-up-metrics"></a>Konfigurera mått 

Konfigurera nu vissa mått att hålla utkik efter när meddelanden skickas till hubben. 

1. I inställningsfönstret för IoT-hubben klickar du på alternativet **Mått** i avsnittet **Övervakning**.

2. Klicka på **Last 24 hours (Automatic)** (Senaste 24 timmarna (automatisk)) överst på skärmen. I listrutan som visas väljer du **Last 4 hours** (De senaste 4 timmarna) för **Tidsintervall**, och ställ in **Tidskornighet** på **1 minut**, lokal tid. Klicka på **Verkställ** för att spara inställningarna. 

   ![Skärmbild som visar tidsinställningar för mått.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. Det finns ett måttalternativ som standard. Låt resursgruppen och mått för namnområdet ha kvar standardinställningarna. I listrutan **Mått** väljer du **Telemetry messages sent** (Skickade telemetrimeddelanden). Ställ in **Sammansättning** på **Summa**.

   ![Skärmbild som visar när man lägger till ett mått för skickade telemetrimeddelanden.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. Klicka på **Lägg till mått** för att lägga till ett annat mått i diagrammet. Välj din resursgrupp (**ContosoTestHub**). Under **du Mått** väljer du **Total number of messages used** (Totalt antal använda meddelanden). För **Sammansättning** väljer du **Avg**. 

   Nu visar skärmen minimerade mått för *Telemetry messages sent* (Skickade telemetrimeddelanden), plus det nya måttet för *Total number of messages used* (Totalt antal använda meddelanden).

   ![Skärmbild som visar när man lägger till ett mått för skickade telemetrimeddelanden.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   Klicka på **Fäst vid instrumentpanelen**. Det fästs på instrumentpanelen i Azure-portalen så att du har åtkomst till det igen. Om du inte fäster det på instrumentpanelen behålls inte dina inställningar.

## <a name="set-up-alerts"></a>Konfigurera aviseringar

Gå till hubben i portalen. Klicka på **Resursgrupper**, välj *ContosoResources* och sedan IoT Hub *ContosoTestHub*. 

IoT Hub har inte migrerats till den [mått i Azure Monitor](/azure/azure-monitor/platform/data-collection#metrics) ännu. Du behöver använda [klassiska aviseringar](/azure/azure-monitor/platform/alerts-classic.overview).

1. Under **Övervakning** klickar du på **Aviseringar** som visar huvudskärmen för aviseringen. 

   ![Skärmbild som visar hur du hittar klassiska aviseringar.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. Gå till klassiska aviseringar härifrån genom att klicka på **Visa klassiska aviseringar**. 

    ![Skärmbild som visar skärmen med klassiska aviseringar.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    Fyll i fälten: 

    **Prenumeration**: Lämna fältet inställt på din aktuella prenumeration.

    **Källa**: Ange *Mått* för fältet.

    **Resursgrupp**: Ställ in fältet på din aktuella resursgrupp, *ContosoResources*. 

    **Resurstyp**: Ställ in fältet på IoT Hub. 

    **Resurs**: Välj din IoT-hubb, *ContosoTestHub*.

3. Klicka på **Lägg till måttavisering (klassisk)** för att ställa in en ny avisering.

    Fyll i fälten:

    **Namn**: Ange ett namn för din varningsregel, som *telemetri-meddelanden*.

    **Beskrivning**: Ange en beskrivning av aviseringen, till exempel *avisera när 1000 telemetrimeddelanden har skickats*. 

    **Källa**: Ställ in detta på *Mått*.

    **Prenumeration**, **Resursgrupp** och **Resurs** ska vara inställda på de värden som du valde på skärmen **Visa klassiska aviseringar**. 

    Ställ in **Mått** på *Telemetry messages sent* (Telemetrimeddelanden har skickats).

    ![Skärmbild som visar hur du konfigurerar en klassisk avisering för telemetrimeddelanden som skickas.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. Efter diagrammet anger du följande fält:

   **Villkor**: Ställ in på *Större än*.

   **Tröskelvärde**: Ange 1000.

   **Period**: Ange *Under de senaste 5 minuterna*.

   **Mottagare av aviseringsmejl**: Här anger du din e-postadress. 

   ![Skärmbild som visar nedre halvan av skärmen för aviseringar.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   Spara aviseringen genom att klicka på **OK**. 

5. Ställ nu in en ny avisering för de *sammanlagda antalet använda meddelanden*. Det här måttet är användbart om du vill skicka en avisering när antalet meddelanden som använts närmar sig kvoten för IoT-hubben – så att du vet att hubben snart startar avvisar meddelanden.

   På skärmen **Visa klassiska aviseringar** klickar du på **Lägg till måttavisering (klassisk)** och fyller därefter i fälten i fönstret **Lägg till regel**.

   **Namn**: Ange ett namn för din varningsregel, som *antal-använda-meddelanden*.

   **Beskrivning**: Ange en beskrivning av aviseringen, till exempel *avisera när en kvot närmar sig*.

   **Källa**: Ange *Mått* för fältet.

    **Prenumeration**, **Resursgrupp** och **Resurs** ska vara inställda på de värden som du valde på skärmen **Visa klassiska aviseringar**. 

    Under **Mått** väljer du *Total number of messages used* (Totalt antal använda meddelanden).

6. Under diagrammet fyller du i följande fält:

   **Villkor**: Ställ in på *Större än*.

   **Tröskelvärde**: Ange 1000.

   **Period**: Ställ in fältet på *Under de senaste 5 minuterna*. 

   **Mottagare av aviseringsmejl**: Här anger du din e-postadress. 

   Spara regeln genom att klicka på **OK**. 

5. Du bör nu se två aviseringar i fönstret med klassiska aviseringar: 

   ![Skärmbild som visar skärmen för klassiska aviseringar skärmen med de nya varningsreglerna.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. Stäng aviseringsfönstret. 
    
    Med de här inställningarna får du en avisering när antalet meddelanden som skickats är större än 400 och när det totala antalet meddelanden som används överskrider NUMBER.

## <a name="run-simulated-device-app"></a>Köra en simulerad enhetsapp

Tidigare i avsnittet om skriptkonfiguration konfigurerade du en enhet för att använda en IoT-enhet. I det här avsnittet ska du ladda ned en .NET-konsolapp som simulerar en enhet som skickar ”enhet till molnet”-meddelanden till en IoT Hub.  

Ladda ned lösningen för [IoT-enhetssimuleringen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Länken laddar ned en lagringsplats som innehåller flera program ned. Lösningen du söker finns i iot-hub/Tutorials/Routing/SimulatedDevice/.

Dubbelklicka på lösningsfilen (SimulatedDevice.sln) för att öppna koden i Visual Studio och öppna sedan Program.cs. Ersätt `{iot hub hostname}` med IoT-hubbens värdnamn. Formatet för IoT-hubbens värdnamn är **{iot-hub-name}.azure-devices.net**. För den här självstudien är hubbens värdnamn **ContosoTestHub.azure-devices.net**. Ersätt därefter `{device key}` med enhetsnyckeln du sparade tidigare när du konfigurerade den simulerade enheten. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Kör och testa 

I Program.cs ändrar du `Task.Delay` från 1000 till 10, vilket minskar tiden mellan skickade meddelanden från 1 sekund till 0,01 sekund. När du förkortar den här fördröjningen ökar antalet meddelanden som skickas.

```csharp
await Task.Delay(10);
```

Kör konsolprogrammet. Vänta några minuter (10–15). Du kan se meddelandena som skickats från den simulerade enheten till hubben på konsolskärmen i programmet.

### <a name="see-the-metrics-in-the-portal"></a>Se Mått i portalen

Öppna dina mått från instrumentpanelen. Ändra tidsvärden till *Senaste 30 minuterna* med en tidskornighet på *1 minut*. Det visar telemetrimeddelanden som skickats och det totala antalet meddelanden som använts i diagrammet, med de senaste siffrorna längst ned i diagrammet. 

   ![Skärmbild som visar måtten.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>Se aviseringarna

Gå tillbaka till aviseringar. Klicka på **Resursgrupper**, välj *ContosoResources* och sedan hubben *ContosoTestHub*. På egenskapssidan som visas för hubben väljer du **Aviseringar**, sedan **Visa klassiska aviseringar**. 

När antalet meddelanden som skickas överskrider gränsen börjar du få e-postaviseringar. Om du vill se om det finns några aktiva aviseringar går du till din hubb och väljer **Aviseringar**. Den visar aviseringar som är aktiva, och om det finns några varningar. 

   ![Skärmbild som visar aviseringarna som har aktiverats.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

Klicka på aviseringen för telemetrimeddelanden. Den visar måttresultatet och ett diagram med resultat. E-postmeddelandet som skickas för att varna dig om aviseringen ser ut som på bilden:

   ![Skärmbild av e-postmeddelandet som visar att aviseringarna har aktiverats.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>Se diagnostikloggarna

Du konfigurerar dina diagnostikloggar som ska exporteras till bloblagringen. Gå till din resursgrupp och välj ditt lagringskonto *contosostoragemon*. Välj Blobar och öppna sedan containern *insights-logs-connections*. Granska nedåt tills du kommer till aktuellt datum och välj den senaste filen. 

   ![Skärmbild av att granska nedåt i lagringscontainern för att se diagnostikloggarna.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

Klicka på **Ladda ned** för att hämta och öppna den. Du kan se loggar för enheten som ansluter och kopplar från när de skickar meddelanden till hubben. Här är ett exempel:

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>Rensa resurser 

Om du vill ta bort alla resurser du skapade för den här självstudien tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar du bort IoT-hubben, lagringskontot och själva resursgruppen. Om du har fäst mått på instrumentpanelen måste du ta bort dem manuellt genom att klicka på de tre punkterna i det övre högra hörnet på varje och välja **Ta bort**.

Om du vill ta bort resursgruppen använder du kommandot [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda mått och diagnostikloggar genom att utföra följande uppgifter:

> [!div class="checklist"]
> * Använda Azure C, skapa en IoT-hubb, en simulerad enhet och ett lagringskonto.  
> * Aktivera diagnostikloggar. 
> * Aktivera mätvärden.
> * Konfigurera aviseringar för dessa mått. 
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben. 
> * Kör appen tills aviseringarna börjar utlösas. 
> * Visa måttresultaten och kontrollera diagnostikloggarna. 

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
[Konfigurera dina enheter från en serverdelstjänst](tutorial-device-twins.md)