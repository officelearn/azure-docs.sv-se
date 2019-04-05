---
title: Visa Azure IoT Hub meddelande routningsresultat (.NET) | Microsoft Docs
description: Visa routningsresultat för Azure IoT Hub-meddelande
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 1417ecdaf6a85f491e1accfb9564e27d15e13445
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045841"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Självstudier: Del 2 – Visa routade meddelanden

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regler för routning av meddelanden

Detta är regler för meddelanderoutning; Dessa är inställda i del 1 i den här självstudien och du ser dem att fungera i den andra delen.

|värde |Resultat|
|------|------|
|level="storage" |Skriv till Azure Storage.|
|level="critical" |Skriv till en Service Bus-kö. En logikapp hämtar meddelandet från kön och använder Office 365 för att skicka meddelandet via e-post.|
|standard |Visa dessa data med Power BI.|

Nu kan du skapa de resurser som meddelanden kommer att dirigeras, kör en app för att skicka meddelanden till hubben, och se routning i praktiken.

## <a name="create-a-logic-app"></a>Skapa en logikapp  

Service Bus-kön ska användas för att ta emot meddelanden som har angetts som kritiska. Konfigurera en logikapp som ska övervaka Service Bus-kön och som skickar e-post när ett meddelande läggs till i Service Bus-kön.

1. I den [Azure-portalen](https://portal.azure.com)väljer **+ skapa en resurs**. Skriv **logikapp** i sökrutan och klicka på Retur. I sökresultatet visas, Välj Logikapp och välj sedan **skapa** att fortsätta den **skapa en logikapp** fönstret. Fyll i fälten.

   **Namn**: Det här fältet är logikappens namn. I självstudien används **ContosoLogicApp**.

   **Prenumeration**: Välj din Azure-prenumeration.

   **Resursgrupp**: Välj **Använd befintlig** och välj din resursgrupp. I den här självstudien används **ContosoResources**.

   **Plats**: Använd din plats. I den här självstudien används **USA, västra**.

   **Log Analytics**: Den här växeln bör vara avstängd.

   ![Skapa Logic App-skärmen](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Välj **Skapa**.

2. Gå nu till logikappen. Det enklaste sättet att komma till Logikappen är att välja **resursgrupper**, Välj en resursgrupp (den här självstudien används **ContosoResources**), och välj den Logic App i listan över resurser. Sidan Logikappdesigner visas (du kanske måste rulla över till höger för att se hela sidan). På sidan Logic Apps Designer rulla nedåt tills du ser den panel som säger **tom Logikapp +** och markera den. Fliken standard är ”för du”. Om det här fönstret är tomt, välja **alla** att se alla anslutningsappar och utlösare som är tillgängliga.

3. Välj **Service Bus** i listan över anslutningar.

   ![Lista över anslutningsappar](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. En lista med utlösare visas. Välj **när ett meddelande tas emot i en kö (Komplettera automatiskt) / Service Bus**.

   ![Listan över utlösare för Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Fyll i namnet på anslutningen på nästa skärm. I den här självstudien används **ContosoConnection**.

   ![Hur du konfigurerar anslutningen för Service Bus-kö](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Välj Service Bus-namnområdet. I den här självstudien används **ContosoSBNamespace**. När du väljer namnrymd ber portalen Service Bus-namnrymden att hämta nycklarna. Välj **RootManageSharedAccessKey** och välj **skapa**.

   ![Konfigurationen av anslutningen slutförs](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. På nästa skärm väljer du namnet på kön (den här självstudien använder **contososbqueue**) i listrutan. Du kan använda standardvärdena för resten av fälten.

   ![Alternativ för kö](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Konfigurera nu att åtgärden ska skicka ett e-postmeddelande när ett meddelande kommer till kön. I Logic Apps Designer väljer **+ nytt steg** för att lägga till ett steg, Välj **alla** att se alla tillgängliga alternativ. I den **Välj en åtgärd** fönstret, lokaliserar och markerar **Office 365 Outlook**. På skärmen för utlösare väljer **skicka ett e-postmeddelande / Office 365 Outlook**.  

   ![Office365-alternativ](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Logga in på Office 365-konto att upprätta anslutningen. Om detta tidsgränsen, försök igen. Ange e-postadresser för mottagare av e-postmeddelanden. Ange även ämne och skriv vilket meddelande du vill att mottagaren ska se i brödtexten. För att testa fyller du i din egen e-postadress som mottagare.

   Välj **Lägg till dynamiskt innehåll** att visa innehållet från meddelandet som ska inkluderas. Välj **Innehåll** – det inkluderar meddelandet i e-postmeddelandet.

   ![Postalternativ för e-för logikappen](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Välj **Spara**. Logikappdesignern öppnas.

## <a name="set-up-azure-stream-analytics"></a>Konfigurera Azure Stream Analytics

Om du vill se data i Power BI-visualiseringen konfigurerar du först ett Stream Analytics-jobb för att hämta data. Kom ihåg att endast meddelanden där **nivå** är satt till **normal** skickas till standardslutpunkten och hämtas av Stream Analytics-jobbet för Power BI-visualiseringen.

### <a name="create-the-stream-analytics-job"></a>Skapa Stream Analytics-jobbet

1. I den [Azure-portalen](https://portal.azure.com)väljer **skapa en resurs** > **Internet of Things** > **Stream Analytics-jobbet**.

2. Ange följande information för jobbet.

   **Jobbnamn**: Namnet på jobbet. Namnet måste vara globalt unikt. I självstudien används **contosoJob**.

   **Prenumeration**: Den Azure-prenumeration som du använder för den här självstudien.

   **Resursgrupp**: Använd samma resursgrupp som användes av IoT-hubben. I den här självstudien används **ContosoResources**.

   **Plats**: Använd samma plats som användes i installationsskriptet. I den här självstudien används **USA, västra**.

   ![Skapa stream analytics-jobb](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Välj **skapa** att skapa jobbet. Gå tillbaka till jobbet, markera **resursgrupper**. I den här självstudien används **ContosoResources**. Välj resursgruppen och välj sedan Stream Analytics-jobb i listan över resurser.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

4. Under **Jobbtopologi**väljer **indata**.

5. I den **indata** väljer **Lägg till strömindata** och välj IoT Hub. På skärmen som visas fyller du i följande fält:

   **Indataalias**: I självstudien används **contosoinputs**.

   **Välj IoT Hub från prenumerationen**: Välj det här alternativet för radio-knappen.

   **Prenumeration**: Ange den prenumeration du använder den här självstudien.

   **IoT Hub**: Välj IoT Hub. I självstudien används **ContosoTestHub**.

   **Slutpunkt**: Välj **Meddelanden**. (Om du väljer Åtgärdsövervakning får du telemetridata om IoT-hubben istället för de data du skickar.) 

   **Namn på princip för delad åtkomst**: Välj **iothubowner**. Portalen fyller i Nyckel för princip för delad åtkomst åt dig.

   **Konsumentgrupp**: Välj konsumentgruppen ställa in i steg 1 i den här självstudien. I den här självstudien används **contosoconsumers**.
   
   För resten av fälten accepterar du standardvärdena. 

   ![Konfigurera indata för stream analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Under **Jobbtopologi**väljer **utdata**.

2. I den **utdata** väljer **Lägg till**, och välj sedan **Power BI**. På skärmen som visas fyller du i följande fält:

   **Utdataalias**: Utdatas unika alias. I självstudien används **contosooutputs**. 

   **Namn på datauppsättning**: Namnet på datauppsättningen som ska användas i Power BI. I självstudien används **contosodataset**. 

   **Tabellnamn**: Namnet på tabellen som ska användas i Power BI. I självstudien används **contosotable**.

   Acceptera standardvärdena för resten av fälten.

3. Välj **auktorisera**, och logga in på ditt Power BI-konto. (Det kan ta flera försök).

   ![Konfigurera utdata för stream analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.

2. Ersätt `[YourInputAlias]` med inmatat alias för jobbet. I självstudien används **contosoinputs**.

3. Ersätt `[YourOutputAlias]` med utdataalias för jobbet. I självstudien används **contosooutputs**.

   ![Ställ in frågan för stream analytics-jobbet](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Välj **Spara**.

5. Stäng rutan Fråga. Du kommer tillbaka till vyn över resurser i resursgruppen. Välj ett Stream Analytics-jobb. I den här självstudiekursen kallas det **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics-jobb väljer **starta** > **nu** > **starta**. När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

För att konfigurera Power BI-rapporten behöver du data, så du måste konfigurera Power BI när du har skapat enheten och kör enhetssimuleringsprogrammet.

## <a name="run-simulated-device-app"></a>Kör app för simulerade enheter

I del 1 av självstudien konfigurerar du en enhet att simulera med hjälp av en IoT-enhet. I det här avsnittet ska du ladda ned .NET-konsolapp som simulerar den enhet som skickar meddelanden från enheten till molnet till en IoT-hubb, förutsatt att du inte redan ladda ned appen och resurser i del 1 av självstudien.

Det här programmet skickar meddelanden för varje routningsmetoder för annat meddelande. Det finns också en mapp i download som innehåller den fullständiga Azure Resource Manager-mallen och parameterfilen, samt Azure CLI och PowerShell-skript.

Om du inte ladda ned filerna från lagringsplatsen i steg 1 i den här självstudien, gå vidare och hämta dem från [IoT Enhetssimulering](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Om du väljer den här länken laddar ned en databas med flera program. lösningen som du söker efter är iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Dubbelklicka på lösningsfilen (IoT_SimulatedDevice.sln) för att öppna koden i Visual Studio och öppna Program.cs. Ersätt `{iot hub hostname}` med IoT-hubbens värdnamn. Formatet för IoT-hubbens värdnamn är **{iot-hub-name}.azure-devices.net**. För den här självstudien är hubbens värdnamn **ContosoTestHub.azure-devices.net**. Ersätt därefter `{device key}` med enhetsnyckeln du sparade tidigare när du konfigurerade den simulerade enheten. 

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

   ![De resulterande e-postmeddelandena](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Det här resultatet innebär följande uttryck utvärderas som true. 

   * Routningen till Service Bus-kön fungerar korrekt.
   * Logikappen som hämtar meddelandet från Service Bus-kön fungerar som den ska.
   * Logic App-anslutningsprogrammet till Outlook fungerar som det ska. 

2. I den [Azure-portalen](https://portal.azure.com)väljer **resursgrupper** och välj din resursgrupp. I den här självstudien används **ContosoResources**. Välj lagringskontot, Välj **Blobar**, Välj behållaren. I självstudien används **contosoresults**. Du bör se en mapp, och du kan öka detaljnivån mellan kataloger tills du ser en eller flera filer. Öppna en av dessa filer. De innehåller poster som dirigeras till lagringskontot. 

   ![Resultatet filerna](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Det här resultatet innebär följande instruktion är sant.

   * Routningen till lagringskontot fungerar korrekt.

Under tiden programmet fortfarande körs konfigurerar du Power BI-visualiseringen för att se om meddelandena skickas genom standardroutningen.

## <a name="set-up-the-power-bi-visualizations"></a>Konfigurera Power BI-visualiseringarna

1. Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto.

2. Gå till **Arbetsytor** och välj arbetsytan som du angav när du skapade utdata för Stream Analytics-jobbet. Den här självstudien använder **Min arbetsyta**. 

3. Välj **datauppsättningar**. Om du har inga datauppsättningar, Vänta några minuter och kontrollera igen.

   Du bör se den listade datauppsättningen som du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosodataset**. (Det kan ta 5–10 minuter innan datauppsättningen visas första gången.)

4. Under **åtgärder**, Välj den första ikonen för att skapa en rapport.

   ![Power BI-arbetsyta med åtgärder och rapporten ikon markerat](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Skapa ett linjediagram för att visa realtidstemperatur över tid.

   * På sidan Skapa, lägger du till ett linjediagram genom att välja raddiagramikonen.

     ![Visualiseringar och fält](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * I rutan **Fält** expanderar du tabellen du angav när du skapade utdata för Stream Analytics-jobbet. I självstudien används **contosotable**.

   * Dra **EventEnqueuedUtcTime** till **Axel** i fönstret **Visualiseringar**.

   * Dra **temperatur** till **Värden**.

   Ett linjediagram skapas. X-axeln visar datum och tid i UTC-tidszonen. Y-axeln visar temperatur från sensorn.

6. Skapa ett annat linjediagram om du vill visa realtidsfuktighet över tid. För att konfigurera ett andra diagram följer du samma steg som ovan och placerar **EventEnqueuedUtcTime** på x-axeln och **fuktighet** på y-axeln.

   ![Sista Power BI-rapporten med de två diagrammen](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Välj **spara** att spara rapporten.

Du bör kunna se data i båda diagrammen. Det här resultatet innebär följande påståenden stämmer:

   * Routningen till standardslutpunkten fungerar korrekt.
   * Azure Stream Analytics-jobbet strömmar som det ska.
   * Power BI-visualiseringen är korrekt konfigurerad.

Du kan uppdatera diagram om du vill se de senaste data genom att välja Uppdatera-knappen överst i Power BI-fönstret. 

## <a name="clean-up-resources"></a>Rensa resurser 

Om du vill ta bort alla resurser som du har skapat via båda delarna av den här självstudiekursen tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar det bort IoT-hubben, Service Bus-namnrymden och kön, logikappen, lagringskontot och själva resursgruppen. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Rensa resurser i Power BI-visualiseringen

Logga in på ditt [Power BI](https://powerbi.microsoft.com/)-konto. Gå till din arbetsyta. Den här självstudien använder **Min arbetsyta**. Om du vill ta bort Power BI-visualiseringen, gå till datauppsättningar och välj Papperskorgen kan ikonen om du vill ta bort datauppsättningen. I självstudien används **contosodataset**. När du tar bort datauppsättningen tas även rapporten bort.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Använda Azure CLI för att rensa resurser

Om du vill ta bort resursgruppen använder du kommandot [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` var inställd **ContosoResources** tillbaka i början av den här självstudien.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Använd PowerShell för att rensa resurser

Om du vill ta bort resursgruppen använder du kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` var inställd **ContosoResources** tillbaka i början av den här självstudien.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I de här självstudierna 2 delar du lärt dig hur du använder meddelanderoutning för att dirigera meddelanden från IoT Hub till olika mål genom att utföra följande uppgifter.  

**En del I: Skapa resurser, konfigurera meddelanderoutning**
> [!div class="checklist"]
> * Skapa resurser – en IoT-hubb, ett lagringskonto, en Service Bus-kö och en simulerad enhet.
> * Konfigurera slutpunkter och meddelandevägar i IoT Hub för storage-konto och Service Bus-kö.

**Del II: Skicka meddelanden till hubben, visa routade resultat**
> [!div class="checklist"]
> * Skapa en logikapp som utlöses och skickar e-post när ett meddelande läggs till i Service Bus-kön.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till hubben för de olika dirigeringsalternativen.
> * Skapa en Power BI-visualisering för data som skickas till standardslutpunkten.
> * Visa resultatet ...
> * ...i Service Bus-kön och e-postmeddelandena.
> * ...i lagringskontot.
> * ...i Power BI-visualiseringen.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Konfigurera och använda mått och diagnostik med en IoT-hubb](tutorial-use-metrics-and-diags.md)
