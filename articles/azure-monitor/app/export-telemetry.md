---
title: Löpande export av telemetri från Application Insights | Microsoft Docs
description: Exportera diagnostik och användningsdata till lagringen i Microsoft Azure och hämta den därifrån.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: c2374bd0d67115bdc9fef2b6937f7b087bc581de
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076781"
---
# <a name="export-telemetry-from-application-insights"></a>Exportera telemetri från Application Insights
Om du vill behålla din telemetri under längre tid än kvarhållningsperioden standard? Eller bearbeta dem i något specialiserade sätt? Löpande Export är perfekt för detta. Händelserna som visas i Application Insights-portalen kan exporteras till lagringen i Microsoft Azure i JSON-format. Därifrån kan du hämta dina data och skriver det kod som du behöver bearbetas.  

Innan du konfigurerar löpande export finns några alternativ som du kanske vill överväga:

* Exportera-knappen högst upp på ett mått eller Sök blad kan du överföra tabeller och diagram till ett Excel-kalkylblad.

* [Analytics](../../azure-monitor/app/analytics.md) tillhandahåller ett kraftfullt frågespråk för telemetri. Det kan också exportera resultat.
* Om du vill att [Utforska dina data i Power BI](../../azure-monitor/app/export-power-bi.md ), kan du göra det utan att använda löpande Export.
* Den [dataåtkomst REST API](https://dev.applicationinsights.io/) får du tillgång till din telemetri programmässigt.
* Du kan även använda installationsprogrammet [löpande export via Powershell](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

När löpande Export kopierar dina data till lagring (där det kan vara under så länge du vill), är det fortfarande tillgängligt i Application Insights för den vanliga [kvarhållningsperioden](../../azure-monitor/app/data-retention-privacy.md).

## <a name="setup"></a> Skapa en löpande Export
1. Öppna löpande Export i Application Insights-resurs för din app och välj **Lägg till**:

2. Välj telemetri datatyper som du vill exportera.

3. Skapa eller välj en [Azure storage-konto](../../storage/common/storage-introduction.md) där du vill lagra data. Mer information om storage prisalternativ finns på [officiella prissättningssidan](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Som standard ställs lagringsplatsen till samma geografiska region som Application Insights-resursen. Om du lagrar i en annan region kan det tillkomma kostnader för dataöverföring.

    ![Klicka på Lägg till, exportera mål, Storage-konto, och skapa sedan en ny butik eller välj en befintlig databas](./media/export-telemetry/02-add.png)

4. Skapa eller välj en behållare i storage:

    ![Klicka på Välj händelsetyper](./media/export-telemetry/create-container.png)

När du har skapat din export, startar den ska. Du kan bara hämta data som tas emot när du har skapat exporten.

Det kan finnas en fördröjning på ungefär en timme innan data visas i lagringen.

### <a name="to-edit-continuous-export"></a>Redigera löpande export

Om du vill ändra redigera händelsetyperna senare exporten:

![Klicka på Välj händelsetyper](./media/export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Stoppa löpande export

Klicka på Inaktivera om du vill stoppa exporten. När du klickar på Aktivera igen startas exporten med nya data. Du kommer inte de data som anlänt på portalen medan export har inaktiverats.

Ta bort den om du vill stoppa exporten permanent. Detta tar inte bort dina data från storage.

### <a name="cant-add-or-change-an-export"></a>Det går inte att lägga till eller ändra en export?
* Om du vill lägga till eller ändra export, behöver du behörighet för ägare, deltagare eller Application Insights-deltagare. [Lär dig mer om roller][roles].

## <a name="analyze"></a> Vilka händelser får du?
Exporterade data är råtelemetridata som vi får från ditt program, förutom att vi lägga till platsdata som vi beräknar från klientens IP-adress.

Data som har tagits bort av [sampling](../../azure-monitor/app/sampling.md) ingår inte i den exporterade data.

Det ingår inte andra beräknade mått. Exempelvis kan vi exportera inte genomsnittliga CPU-användningen, men vi exporterar råtelemetridata som från vilken medelvärdet beräknas.

Informationen omfattar även resultaten av eventuella [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md) som du har konfigurerat.

> [!NOTE]
> **Sampling.** Om ditt program skickar stora mängder data, kan sampling-funktionen fungerar och skicka bara en bråkdel av telemetri som genereras. [Läs mer om sampling.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Granska data
Du kan granska lagring direkt i portalen. Klicka på **Bläddra**, Välj ditt lagringskonto och öppna sedan **behållare**.

Om du vill inspektera Azure storage i Visual Studio, öppna **visa**, **Cloud Explorer**. (Om du inte har det menykommandot, måste du installera Azure SDK: Öppna den **nytt projekt** dialogrutan Expandera Visual C#/i molnet och välj **få Microsoft Azure SDK för .NET**.)

När du öppnar din blob store, visas en behållare med en uppsättning blobfiler. URI: N för varje fil som härleds från din Application Insights-resurs med instrumenteringsnyckeln, telemetri-typ/datum/tid. (Resursnamnet är gemener och instrumenteringsnyckeln utesluter tankstreck.)

![Granska blob store med ett lämpligt verktyg](./media/export-telemetry/04-data.png)

Datum och tid är UTC och när telemetri har deponerats i store - inte den tid det skapades. Så om du skriver kod för att hämta data, kan det flytta linjärt via data.

Här är form av sökvägen:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Var

* `blobCreationTimeUtc` tid när blob skapades i den interna är Förproduktion storage
* `blobDeliveryTimeUtc` är den tid när blob kopieras till mållagret export

## <a name="format"></a> Dataformat
* Varje blob är en textfil som innehåller flera ' \n'-separated rader. Den innehåller den telemetri som bearbetas under en tidsperiod för ungefär en halv minut.
* Varje rad motsvarar en datapunkt i telemetri, till exempel en förfrågan eller sidvisning.
* Varje rad är en oformaterad JSON-dokument. Om du vill sitta och vårvädret på det, öppna den i Visual Studio och väljer Redigera, Avancerat formatfil:

![Visa telemetri med ett lämpligt verktyg](./media/export-telemetry/06-json.png)

Tidsvaraktigheter finns i ticks, där 10 000 markeringar = 1 ms. Till exempel de här värdena visas en tid på 1 ms att skicka en begäran från webbläsaren, 3 ms att ta emot och 1.8 s för att bearbeta sidan i webbläsaren:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Detaljerade data modell-referens för egenskapstyper och värden.](export-data-model.md)

## <a name="processing-the-data"></a>Bearbetning av data
I liten skala, kan du skriva kod för att sätta ihop dina data, läsa den i ett kalkylblad och så vidare. Exempel:

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

Ett större kodexempel Se [med hjälp av en arbetsroll][exportasa].

## <a name="delete"></a>Ta bort din gamla data
Observera att du är ansvarig för att hantera lagringskapaciteten och ta bort gamla data om det behövs.

## <a name="if-you-regenerate-your-storage-key"></a>Om du återskapar din lagringsnyckel...
Om du ändrar nyckeln till din lagring att löpande export sluta fungera. Du ser ett meddelande i ditt Azure-konto.

Öppna bladet löpande Export och redigera exporten. Redigera exportera mål, men lämnar du bara samma lagring som valts. Klicka på OK för att bekräfta.

![Redigera den löpande exporten, öppna och stänga dig exportmål.](./media/export-telemetry/07-resetstore.png)

Den löpande exporten startas om.

## <a name="export-samples"></a>Export-exempel

* [Exportera till SQL med Stream Analytics][exportasa]
* [Stream Analytics-exempel 2](export-stream-analytics.md)

På större skalar du [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop-kluster i molnet. HDInsight erbjuder en mängd olika tekniker för att hantera och analysera stora datamängder och du kan använda den för att bearbeta data som har exporterats från Application Insights.

## <a name="q--a"></a>Frågor och svar
* *Men jag vill bara en engångshämtning i ett diagram.*  

    Ja, kan du göra det. Överst på bladet klickar du på **exportera Data**.
* *Jag har skapat en export, men det finns inga data i mitt Arkiv.*

    Application Insights fick någon telemetri från din app eftersom du har konfigurerat export? Du får endast nya data.
* *Jag försökte ställa in en export, men nekades åtkomst*

    Om konton som ägs av din organisation, måste du vara medlem i gruppen ägare eller deltagare.
* *Kan jag exportera direkt till min egen lokal databas?*

    Nej, tyvärr. Vår motor för export fungerar för närvarande bara med Azure storage just nu.  
* *Finns det någon gräns för mängden data som du placerar i mitt Arkiv?*

    Nej. Vi kommer att skicka data tills du tar bort exporten. Vi slutar om vi når yttre gränserna för blob storage, men det är ganska stora. Det är upp till dig att styra hur mycket lagringsutrymme du använder.  
* *Hur många blobar ska visas i storage?*

  * För varje datatyp som du har valt för att exportera, skapas en ny blob varje minut (om det finns data).
  * För program med hög belastning, dessutom tilldelas ytterligare partition enheter. I det här fallet skapar varje enhet en blob i minuten.
* *Jag nyckeln har återskapats till min lagring eller ändrat namnet för behållaren och nu exporten fungerar inte.*

    Redigera exporten och öppna bladet export mål. Lämna samma lagringsutrymme som tidigare har valt och klicka på OK för att bekräfta. Exportera startas om. Om ändringen var inom de senaste dagarna, förlorar data.
* *Kan jag pausar export?*

    Ja. Klicka på Inaktivera.

## <a name="code-samples"></a>Kodexempel

* [Stream Analytics-exempel](export-stream-analytics.md)
* [Exportera till SQL med Stream Analytics][exportasa]
* [Detaljerade data modell-referens för egenskapstyper och värden.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
