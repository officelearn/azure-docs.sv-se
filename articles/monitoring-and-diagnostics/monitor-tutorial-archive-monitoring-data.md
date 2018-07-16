---
title: Arkivera Azure mått och loggdata med hjälp av Azure Storage
description: Arkivera loggen och mätdata som genereras i Azure i ett lagringskonto.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.component: metrics
ms.openlocfilehash: f6b7b9fe73f5e815e08bbf4f6493ee181a0c692b
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918279"
---
# <a name="archive-azure-monitoring-data"></a>Arkivera Azure-övervakningsdata

Flera lager i Azure-miljön producerar logg- och måttdata som kan arkiveras i ett Azure Storage-konto. Du kanske vill bevara historik för övervakningsdata i en kostnadseffektiv men icke sökbar lagring när dess data har passerat bevarandeperioden i Log Analytics och Azure Monitor. Här leder vi dig genom processen med att konfigurera Azure-miljön så att du kan arkivera data på ett lagringskonto.

> [!div class="checklist"]
> * Skapa ett lagringskonto och lagra övervakningsdata
> * Dirigera prenumerationsloggar
> * Dirigera resursdata
> * Dirigera data från virtuell dator (gästoperativsystem)
> * Visa övervakningsdata
> * Rensa dina resurser

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Du måste först skapa ett lagringskonto där övervakningsdata arkiveras. För att göra det [följer du de här stegen](../storage/common/storage-create-storage-account.md).

## <a name="route-subscription-logs-to-the-storage-account"></a>Dirigera prenumerationsloggar till lagringskontot

Du kan nu börja konfigurera Azure-miljön så att du kan skicka övervakningsdata till ett lagringskonto. Vi börjar med att konfigurera data på prenumerationsnivå (i Azure-aktivitetsloggen) så att de dirigeras till lagringskontot. [**Azure-aktivitetsloggen**](monitoring-overview-activity-logs.md) innehåller historik över händelser på prenumerationsnivå i Azure. Du kan bläddra i Azure-portalen för att fastställa *vem som* skapat, uppdaterat och tagit bort *vilka* resurser och *när* de gjorde det.

1. Klicka på knappen **Övervaka** i vänster navigeringslista. Klicka sedan på **Aktivitetslogg**.

   ![Aktivitetsloggavsnittet](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. I aktivitetsloggavsnittet som visas klickar du på knappen **Exportera**.

3. I avsnittet **Exportera aktivitetslogg** kryssar du i **Exportera till ett lagringskonto** och klickar på **Välj ett lagringskonto.**

   ![Export av aktivitetslogg](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. I avsnittet som visas använder du listrutan **Lagringskonto** och väljer namnet på det lagringskonto du skapade i det föregående steget **Skapa ett lagringskonto**. Klicka på **OK**.

   ![Välj ett lagringskonto](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. Ställ in reglaget **Bevarande (dagar)** till 30. Det här reglaget anger hur många dagar som övervakningsdata behålls på lagringskontot. Azure Monitor avlägsnar automatiskt data som är äldre än det angivna antalet dagar. Om du ställer in noll dagar lagras data utan tidsbegränsning.

6. Klicka på **Spara** och stäng avsnittet.

Nu flödar övervakningsdata från prenumerationen till lagringskontot.

## <a name="route-resource-data-to-the-storage-account"></a>Dirigera resursdata till lagringskontot

Nu konfigurerar vi resursnivådata (resursmått och diagnostikloggar) så att de dirigeras till lagringskontot genom att skapa **resursdiagnostikinställningar**.

1. Klicka på knappen **Övervaka** i vänster navigeringslista. Klicka sedan på **Diagnostikinställningar**. Här ser du en lista över alla resurser i prenumerationen som producerar övervakningsdata via Azure Monitor. Om du inte har några resurser i listan kan du [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md) innan du fortsätter, så att du har en resurs som du kan konfigurera en diagnostikinställning på.

2. Klicka på en resurs i listan och klicka sedan på **Slå på diagnostik**.

   ![Slå på diagnostik](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   Om det redan finns en konfigurerad inställning ser du i stället de befintliga inställningarna och en knapp för att **lägga till diagnostikinställningar**. Klicka på knappen.

   En resursdiagnostikinställning är en definition av *vilka* övervakningsdata som ska dirigeras från en viss resurs och *vart* dessa övervakningsdata ska dirigeras.

3. I avsnittet som visas ger du inställningen ett **namn** och kryssar i rutan för att **Arkivera till ett lagringskonto**.

   ![Avsnittet diagnostikinställningar](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. Klicka på knappen **Konfigurera** under **Arkivera till ett lagringskonto** och välj det lagringskonto du skapade i föregående avsnitt. Klicka på **OK**.

   ![Diagnostikinställningar för lagringskonto](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. Markera alla rutor under **Logg** och **Mått**. Beroende på resurstyp kanske du bara ser ett av alternativen. De här kryssrutorna styr vilka kategorier av för resurstypen tillgängliga logg- och måttdata som skickas till den valda målplatsen, i det här fallet ett lagringskonto.

   ![Kategorier för diagnostikinställningar](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)

6. Ställ in reglaget **Bevarande (dagar)** till 30. Det här reglaget anger hur många dagar som övervakningsdata behålls på lagringskontot. Azure Monitor avlägsnar automatiskt data som är äldre än det angivna antalet dagar. Om du ställer in noll dagar lagras data utan tidsbegränsning.

7. Klicka på **Spara**.

Nu flödar övervakningsdata från resursen till lagringskontot.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>Dirigera data från virtuell dator (gästoperativsystem) till lagringskontot

1. Om du inte redan har en virtuell dator i prenumerationen [skapar du en virtuell dator](../virtual-machines/windows/quick-create-portal.md).

2. I den vänstra navigeringslistan i portalen klickar du på **virtuella datorer**.

3. I listan med virtuella datorer som visas klickar du på den som du skapade.

4. I avsnittet som visas klickar du på **Diagnostikinställningar** i navigeringen till vänster. Via det här avsnittet kan du konfigurera övervakningstillägget från Azure Monitor på den virtuella datorn och dirigera data som produceras av Windows eller Linux till ett lagringskonto.

   ![Navigera till diagnostiska inställningar](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. Klicka på **Aktivera övervakning av gästnivå** i avsnittet som visas.

   ![Aktivera diagnostikinställningar](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. När diagnostikinställningen har sparats visas en lista över de data som samlas in och var de lagras på fliken **Översikt**. Klicka på avsnittet **Prestandaräknare** och granska de Windows-prestandaräknare som samlas in.

   ![Inställningar för prestandaräknare](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)

7. Klicka på fliken **Loggar** och markera kryssrutorna för loggar på nivån **Information** i program- och systemloggar.

   ![Logginställningar](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. Klicka på fliken **Agent**. Under **Lagringskonto** klickar du på namnet på det lagringskonto som visas.

   ![Uppdatera lagringskonto](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. I avsnittet som visas väljer du det lagringskonto som du skapade i föregående steg i **Skapa ett lagringskonto**.

10. Klicka på **Spara**.

Nu flödar övervakningsdata från de virtuella datorerna till lagringskontot.

## <a name="view-the-monitoring-data-in-the-storage-account"></a>Visa övervakningsdata i lagringskontot

> [!WARNING]
> Formatet för loggdata i lagringskontot ändras till JSON Lines den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

Om du har följt föregående steg har data börjat flöda till lagringskontot.

1. För vissa datatyper, till exempel aktivitetsloggen, måste det finnas viss aktivitet som genererar en händelse i lagringskontot. Följ [de här instruktionerna](./monitor-quick-audit-notify-action-in-subscription.md) för att skapa aktiviteten i aktivitetsloggen. Du kan behöva vänta upp till fem minuter innan händelsen visas på lagringskontot.

2. I portalen navigerar du till avsnittet **Lagringskonton** genom att söka i navigeringsfältet till vänster.

3. Identifiera det lagringskonto som du skapade i föregående avsnitt och klicka på det.

4. Klicka på **Blobar**och sedan på behållaren med etiketten **insights-operational-logs** och slutligen på behållaren märkt **name=default**. Det här är den behållare som innehåller aktivitetsloggen. Övervakningsdata bryts ned i behållare efter resurs-ID (bara prenumerations-ID för aktivitetsloggen) och sedan datum och tid. Fullständigt format för dessa blobar är:

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{prenumerations-ID}/y={fyrsiffrigt årtal}/m={månad med två siffror}/d={dag med två siffror}/h={24-timmarsklocka med två siffror}/m=00/PT1H.json

5. Navigera till filen PT1H.json genom att klicka på behållarna för resurs-ID, datum och tid. Klicka på filen PT1H.json och på **Hämta**. Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minutvärdet (m=00) är alltid 00, eftersom logghändelser delas upp i enskilda blobar per timme.

   Du kan nu visa JSON-händelsen som lagrats i lagringskontot. Formatet för blobarna för resursdiagnostikloggar är:

   insights-logs-{namn på loggkategori}/resourceId=/{resurs-ID}/y={fyrsiffrigt årtal}/m={månad med två siffror}/d={dag med två siffror}/h={24-timmarsklocka med två siffror}/m=00/PT1H.json

6. Övervakningsdata för gästoperativsystem lagras i tabeller. Navigera tillbaka till startsidan för lagringskontot och klicka på **Tabeller**. Det finns tabeller för mått, prestandaräknare och händelseloggar.

Du har nu konfigurerat övervakningsdata så att de arkiveras på ett lagringskonto.

## <a name="clean-up-resources"></a>Rensa resurser

1. Navigera tillbaka till avsnittet **Exportera aktivitetslogg** från föregående steg i **Dirigera prenumerationsloggar till lagringskontot**. Klicka på **Återställ**.

2. Navigera till avsnittet **Diagnostikinställningar**. Klicka på den resurs på vilken du skapade en diagnostikinställning i det föregående steget **Dirigera resursdata till lagringskontot**. Hitta de inställningar du skapade, klicka på knappen **Redigera inställning** och klicka på **Ta bort**.

3. Navigera till avsnittet **Diagnostikinställningar** på den virtuella dator som du konfigurerade i det föregående steget **Dirigera data från virtuell dator (gästoperativsystem) till lagringskontot**. På fliken **Agent** klickar du på **Ta bort** (under avsnittet **Ta bort Azure-diagnostikagent**).

4. Navigera till det lagringskonto som du skapade i det föregående steget **Skapa ett lagringskonto**. Klicka på **Ta bort lagringskonto**. Ange namnet på lagringskontot och klicka sedan på **Ta bort**.

5. Om du skapade en virtuell dator eller logikapp i föregående steg tar du bort dem också.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du har lärt dig hur du ställer in så att övervakningsdata från Azure-miljön (prenumeration, resurs och gästoperativsystem) arkiveras på ett lagringskonto.


> [!div class="checklist"]
> * Skapa ett lagringskonto och lagra övervakningsdata
> * Dirigera prenumerationsloggar
> * Dirigera resursdata
> * Dirigera data från virtuell dator (gästoperativsystem)
> * Visa övervakningsdata
> * Rensa dina resurser

Om du vill få ut ännu mer av dina data ska du även skicka dem till Log Analytics.

> [!div class="nextstepaction"]
> [Komma igång med Log Analytics](../log-analytics/log-analytics-get-started.md)
