---
title: Löpande export av telemetri från Application Insights | Microsoft Docs
description: Exportera diagnostik och användningsdata till Microsoft Azure-lagring och ladda ned den därifrån.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: mbullwin
ms.openlocfilehash: 05d271eb7d046819bb8fc2be20623cba0000d8f4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
---
# <a name="export-telemetry-from-application-insights"></a>Exportera telemetri från Application Insights
Om du vill behålla din telemetri under längre tid än standard kvarhållningsperioden? Eller bearbeta några särskilda sätt? Löpande Export är idealisk för detta. Händelserna som visas i Application Insights-portalen kan exporteras till lagring i Microsoft Azure i JSON-format. Därifrån kan du hämta data och skriva oavsett kod som du behöver för att bearbeta den.  

Med hjälp av löpande Export kan innebära en extra kostnad. Kontrollera din [Prismodell](http://azure.microsoft.com/pricing/details/application-insights/).

Innan du konfigurerar löpande export finns det några alternativ som du kanske vill överväga:

* Exportera-knappen längst upp på ett mått eller Sök blad kan du överföra tabeller och diagram till en Excel-kalkylblad.

* [Analytics](app-insights-analytics.md) tillhandahåller kraftfulla frågespråk för telemetri. Det kan också exportera resultaten.
* Om du tittar på [Utforska dina data i Power BI](app-insights-export-power-bi.md), kan du göra det utan att använda löpande Export.
* Den [REST API för dataåtkomst](https://dev.applicationinsights.io/) kan du komma åt din telemetri programmässigt.
* Du kan också använda installationsprogrammet [löpande export via Powershell](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

När löpande Export kopierar data till lagring (där den kan hålla för så länge du vill), är det fortfarande tillgängliga i Application Insights för de vanliga [kvarhållningsperioden](app-insights-data-retention-privacy.md).

## <a name="setup"></a> Skapa en löpande Export
1. Öppna löpande Export i Application Insights-resurs för din app och välj **Lägg till**:

    ![Bläddra nedåt och klicka på löpande Export](./media/app-insights-export-telemetry/01-export.png)

2. Välj telemetrin datatyper som du vill exportera.

3. Skapa eller välj en [Azure storage-konto](../storage/common/storage-introduction.md) där du vill lagra data.

    > [!Warning]
    > Som standard kommer lagringsplatsen anges till samma geografiska region som Application Insights-resurs. Om du sparar i en annan region, kan det utgå avgifter för överföring.

    ![Klicka på Lägg till, exportera mål, Storage-konto och sedan skapa en ny lagring eller välj en befintlig butik](./media/app-insights-export-telemetry/02-add.png)

4. Skapa eller välj en behållare i lagring:

    ![Klicka på Välj händelsetyper](./media/app-insights-export-telemetry/create-container.png)

När du har skapat din export, startar det ska. Du kan bara hämta data som tas emot när du har skapat exporten.

Det kan finnas en fördröjning på ungefär en timme innan data visas i lagringen.

### <a name="to-edit-continuous-export"></a>Så här redigerar du löpande export

Om du vill ändra redigera händelsetyperna senare kan du bara exporten:

![Klicka på Välj händelsetyper](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Stoppa löpande export

Klicka på Inaktivera om du vill stoppa exporten. När du klickar på Aktivera igen startas exporten med nya data. Du kommer inte de data som anlänt på portalen medan export har inaktiverats.

Ta bort den om du vill stoppa exporten permanent. Gör det bort inte data från lagring.

### <a name="cant-add-or-change-an-export"></a>Det går inte att lägga till eller ändra en export?
* Om du vill lägga till eller ändra export, behöver du ägare, deltagare eller Application Insights deltagare åtkomsträttigheter. [Lär dig mer om roller][roles].

## <a name="analyze"></a> Vilka händelser får du?
Exporterade data är raw telemetri som vi får från programmet, förutom att vi lägga till lokaliseringsuppgifter som vi beräkna från klientens IP-adress.

Data som har tagits bort av [provtagning](app-insights-sampling.md) ingår inte i den exporterade data.

Det ingår inte andra beräknade mått. Till exempel vi exportera inte Genomsnittlig CPU-användning, men vi exporterar rådata telemetri som medelvärdet beräknas.

Informationen omfattar även resultatet av alla [tillgänglighet webbtester](app-insights-monitor-web-app-availability.md) som du har skapat.

> [!NOTE]
> **Sampling.** Om ditt program skickar stora mängder data, funktionen provtagning fungerar och skicka en bråkdel av genererade telemetri. [Läs mer om sampling.](app-insights-sampling.md)
>
>

## <a name="get"></a> Granska data
Du kan inspektera lagring direkt på portalen. Klicka på **Bläddra**, Välj ditt lagringskonto och öppna sedan **behållare**.

Om du vill granska Azure storage i Visual Studio, öppna **visa**, **Cloud Explorer**. (Om du inte har det menykommandot, måste du installera Azure SDK: öppna den **nytt projekt** dialogrutan Expandera Visual C# / molnet och välj **hämta Microsoft Azure SDK för .NET**.)

När du öppnar din blobstore, visas en behållare med en uppsättning blob-filer. URI för varje fil som härletts från din Application Insights-resurs med instrumentation nyckel, telemetri-typ/datum/tid. (Resursnamnet är gemener och nyckeln instrumentation utesluter bindestreck.)

![Inspektera blobstore med ett lämpligt verktyg](./media/app-insights-export-telemetry/04-data.png)

Datum och tid är UTC och när telemetrin har deponerats i store - inte den tid det skapades. Så om du skriva kod för att ladda ned data flytta den linjärt-informationen.

Här är formatet sökvägen:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Där

* `blobCreationTimeUtc` tid då blob skapades i det interna är Förproduktion storage
* `blobDeliveryTimeUtc` är den tid när blob kopieras till mållagringskontot export

## <a name="format"></a> Dataformatet
* Varje blobb är en textfil som innehåller flera ' \n'-separated rader. Den innehåller telemetri bearbetas under en period av ungefär en halv minut.
* Varje rad representerar en telemetri data, till exempel en vy för begäran eller sidan.
* Varje rad är ett oformaterat JSON-dokument. Om du vill sitta och vårvädret den, öppna den i Visual Studio och väljer Redigera, Avancerat formatfilen:

![Visa telemetri med ett lämpligt verktyg](./media/app-insights-export-telemetry/06-json.png)

Tidsvaraktigheter finns i tick, där 10 000 markeringar = 1 ms. Till exempel värdena visa taget 1 ms att skicka en begäran från webbläsaren, 3 ms för att ta emot och 1.8 s för att bearbeta sida i webbläsaren:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Detaljerad datamodell referens för egenskapstyper och värden.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Bearbetning av data
I liten skala, kan du skriva kod för att sätta ihop dina data, läsa in den i ett kalkylblad och så vidare. Exempel:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

En större kodexemplet finns [med hjälp av en arbetsroll][exportasa].

## <a name="delete"></a>Ta bort gamla data
Observera att du är ansvarig för att hantera lagringskapaciteten och ta bort gamla data om det behövs.

## <a name="if-you-regenerate-your-storage-key"></a>Om du återskapar nyckeln för säkerhetslagring...
Om du ändrar nyckeln till din lagring att löpande export sluta fungera. Du ser ett meddelande i ditt Azure-konto.

Öppna bladet löpande Export och redigera exporten. Redigera exportera mål, men lämna samma lagringsutrymme som valts. Klicka på OK för att bekräfta.

![Redigera den löpande exporten, öppna och stänga dig exportera mål.](./media/app-insights-export-telemetry/07-resetstore.png)

Den löpande exporten startas om.

## <a name="export-samples"></a>Export-exempel

* [Exportera till SQL med Stream Analytics][exportasa]
* [Stream Analytics-exempel 2](app-insights-export-stream-analytics.md)

Överväg att i större skala [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-kluster i molnet. HDInsight tillhandahåller en mängd olika tekniker för att hantera och analys av stordata och du kan använda för att bearbeta data som har exporterats från Application Insights.

## <a name="q--a"></a>Frågor och svar
* *Men jag vill bara en gång hämtning av ett diagram.*  

    Ja, kan du göra det. Överst på bladet klickar du på **exportera Data**.
* *Jag har skapat en export, men det finns inga data i mitt Arkiv.*

    Application Insights erhöll någon telemetri från din app eftersom du ställer in exporten? Du får endast nya data.
* *Jag försökte ställa in en export men nekas åtkomst*

    Du måste vara medlem i grupperna ägare eller deltagare om kontot ägs av organisationen.
* *Kan jag exportera direkt till min egen lokala store?*

    Nej, tyvärr. Vår export-motorn fungerar för tillfället bara med Azure storage just nu.  
* *Finns det någon gräns för hur mycket data som du lägger till i mitt Arkiv?*

    Nej. Vi kommer att skicka data tills du tar bort exporten. Vi ska avbrytas om vi träffar yttre gränser för blob-lagring, men det är ganska stor. Det är upp till dig att styra hur mycket lagringsutrymme som du använder.  
* *Hur många blobbar ska visas i lagringen?*

  * För varje datatyp som du har valt för att exportera, skapas en ny blob minuten (om det finns data).
  * Dessutom för program med hög belastning på ytterligare partition enheter allokeras. I det här fallet skapar varje enhet en blob varje minut.
* *Jag återskapas nyckeln till min lagring eller ändrat namnet för behållaren och nu exporten fungerar inte.*

    Redigera exporten och öppna bladet exportera mål. Lämna samma lagringsutrymme som tidigare valt och klicka på OK för att bekräfta. Exportera startas om. Om ändringen var inom de senaste dagarna, kan du inte förlora data.
* *Kan jag pausa exporten?*

    Ja. Klicka på Inaktivera.

## <a name="code-samples"></a>Kodexempel

* [Stream Analytics-exempel](app-insights-export-stream-analytics.md)
* [Exportera till SQL med Stream Analytics][exportasa]
* [Detaljerad datamodell referens för egenskapstyper och värden.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
