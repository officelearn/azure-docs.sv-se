---
title: "Exportera med Stream Analytics från Azure Application Insights | Microsoft Docs"
description: "Stream Analytics kontinuerligt transformera, filtrera och dirigera data som du exporterar från Application Insights."
services: application-insights
documentationcenter: 
author: noamben
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: mbullwin
ms.openlocfilehash: 978af1a57a5fc3d9c95d517288a074c636874984
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Använda Stream Analytics för att bearbeta data som har exporterats från Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) är det perfekta verktyget för databearbetning [exporterats från Application Insights](app-insights-export-telemetry.md). Stream Analytics kan hämta data från olika källor. Det kan omvandla filtrera data och sedan dirigera till en mängd olika sänkor.

I det här exemplet ska vi skapa en adapter som hämtar data från Application Insights, byter namn på och behandlar vissa fält och kommer till Power BI.

> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](app-insights-export-power-bi.md). Sökvägen som visas här är bara ett exempel som illustrerar hur du bearbetar exporterade data.
> 
> 

![Blockdiagram för export via SA till PBI](./media/app-insights-export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Löpande export alltid matar ut data till ett Azure Storage-konto, så måste du först skapa lagring.

1. Skapa ett ”klassisk” storage-konto i din prenumeration i den [Azure-portalen](https://portal.azure.com).
   
   ![Välj ny, Data, lagring i Azure-portalen](./media/app-insights-export-stream-analytics/030.png)
2. Skapa en behållare
   
    ![I det nya lagringsutrymmet Välj behållare, klicka på panelen behållare och Lägg till](./media/app-insights-export-stream-analytics/040.png)
3. Kopiera lagringsåtkomstnyckel
   
    Du behöver den snart att ställa in indata till stream analytics-tjänsten.
   
    ![I lagringen, öppna inställningarna för nycklar, och ta en kopia av den primära åtkomstnyckeln](./media/app-insights-export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta löpande export till Azure-lagring
[Löpande export](app-insights-export-telemetry.md) flyttar data från Application Insights i Azure storage.

1. Bläddra till Application Insights-resurs som du har skapat för ditt program i Azure-portalen.
   
    ![Klicka på Bläddra, Application Insights för ditt program](./media/app-insights-export-stream-analytics/050.png)
2. Skapa en löpande export.
   
    ![Välj Lägg till inställningar för löpande Export](./media/app-insights-export-stream-analytics/060.png)

    Välj lagringskontot som du skapade tidigare:

    ![Ange mål för export](./media/app-insights-export-stream-analytics/070.png)

    Ange händelsetyper som du vill visa:

    ![Välj händelsetyper](./media/app-insights-export-stream-analytics/080.png)

1. Kan vissa data ackumuleras. Sitta tillbaka och låta personer använder programmet ett tag. Telemetri kommer så ser du statistiska diagram i [mått explorer](app-insights-metrics-explorer.md) och enskilda händelser i [diagnostiska Sök](app-insights-diagnostic-search.md). 
   
    Och även exporteras data till din lagring. 
2. Granska de exportera data. I Visual Studio väljer **visa / Cloud Explorer**, och öppna Azure / lagring. (Om du inte har alternativet, måste du installera Azure SDK: öppna dialogrutan Nytt projekt och Visual C# / molnet / hämta Microsoft Azure SDK för .NET.)
   
    ![](./media/app-insights-export-stream-analytics/04-data.png)
   
    Anteckna den vanliga del av sökvägen som härleds från programmet namn och instrumentation nyckeln. 

Händelser skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Vi vill så att läsa informationen om händelsen och filtrera bort de fält som vi vill. Det finns många olika sätt som vi kan göra med data, men vår plan är idag att använda Stream Analytics för att skicka data till Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans
Från den [klassiska Azure-portalen](https://manage.windowsazure.com/), Välj Azure Stream Analytics-tjänsten och skapa ett nytt Stream Analytics-jobb:

![](./media/app-insights-export-stream-analytics/090.png)

![](./media/app-insights-export-stream-analytics/100.png)

När det nya jobbet skapas, expandera information:

![](./media/app-insights-export-stream-analytics/110.png)

### <a name="set-blob-location"></a>Ange blob-plats
Ställ in den att hämta indata från din löpande Export blob:

![](./media/app-insights-export-stream-analytics/120.png)

Nu behöver du den primära åtkomstnyckeln från ditt Lagringskonto som du antecknade tidigare. Ange som Lagringskontots åtkomstnyckel.

![](./media/app-insights-export-stream-analytics/130.png)

### <a name="set-path-prefix-pattern"></a>Ange sökvägar för prefix
![](./media/app-insights-export-stream-analytics/140.png)

**Glöm inte att ange datumformatet för åååå-MM-DD (med bindestreck).**

Prefixet sökvägar anger om Stream Analytics finner indatafilerna i lagringen. Du måste konfigurera den så att de motsvarar hur löpande Export lagrar data. Ställ in den så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27`är namnet på Application Insights-resursen **alla gemen**.
* `1234...`är instrumentation nyckel för Application Insights-resurs **utesluter streck**. 
* `PageViews`är typ av data som du vill analysera. Tillgängliga typer är beroende av filtret som du anger i löpande Export. Granska exporterade data för att se de tillgängliga typerna och se den [exportera datamodellen](app-insights-export-data-model.md).
* `/{date}/{time}`ett mönster skrivs bokstavligt.

> [!NOTE]
> Kontrollera lagringen för att kontrollera att du få sökvägen.
> 
> 

### <a name="finish-initial-setup"></a>Slut installationen
Bekräfta serialiseringsformat:

![Bekräfta och Stäng guiden](./media/app-insights-export-stream-analytics/150.png)

Stäng guiden och vänta på att installationen ska kunna slutföras.

> [!TIP]
> Använd kommandot exempel för att hämta vissa data. Behåll det som ett prov för felsökning av din fråga.
> 
> 

## <a name="set-the-output"></a>Ange utdata
Välj ditt jobb och ange utdata.

![Välj den nya kanalen, klicka på utdata, Lägg till, Power BI](./media/app-insights-export-stream-analytics/160.png)

Ange din **arbets- eller skolkonto** att auktorisera Stream Analytics åtkomst till Power BI-resursen. Skriv sedan ett namn för utdata och för mål Power BI dataset och tabell.

![Tjänsten tre namn](./media/app-insights-export-stream-analytics/170.png)

## <a name="set-the-query"></a>Skapa en fråga
Frågan styr översättning från indata till utdata.

![Markera jobbet och klicka på frågan. Klistra in exemplet nedan.](./media/app-insights-export-stream-analytics/180.png)

Använd funktionen Test för att kontrollera att du får rätt utdata. Ge den exempeldata som du tog från sidan indata. 

### <a name="query-to-display-counts-of-events"></a>Fråga om du vill visa antalet händelser
Klistra in den här frågan:

```SQL

    SELECT
      flat.ArrayValue.name,
      count(*)
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.[event]) as flat
    GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-indata är det alias som vi gav dataströmmen indata
* pbi-utdata är kolumnalias som vi har definierat
* Vi använder [yttre gäller GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) eftersom händelsenamnet i en kapslad JSON-arrray. Välj hämtar sedan händelsenamnet, tillsammans med en uppräkning av antalet instanser med det namnet i tidsperioden. Den [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) satsen grupper elementen på perioder på 1 minut.

### <a name="query-to-display-metric-values"></a>Frågan för att visa måttvärden
```SQL

    SELECT
      A.context.data.eventtime,
      avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
    INTO
      [pbi-output]
    FROM
      [export-input] A
    OUTER APPLY GetElements(A.context.custom.metrics) as flat
    GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime

``` 

* Den här frågan går till mått-telemetri för att hämta tiden och måttet. Mått värden är i en matris, så vi använder yttre gäller GetElements mönster för att extrahera rader. ”myMetric” är namnet på måttet i det här fallet. 

### <a name="query-to-include-values-of-dimension-properties"></a>Frågan ska innehålla värden för dimensionsegenskaper
```SQL

    WITH flat AS (
    SELECT
      MySource.context.data.eventTime as eventTime,
      InstanceId = MyDimension.ArrayValue.InstanceId.value,
      BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
    FROM MySource
    OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
    )
    SELECT
     eventTime,
     InstanceId,
     BusinessUnitId
    INTO AIOutput
    FROM flat

```

* Den här frågan innehåller värden för dimensionsegenskaperna utan beroende på en viss dimension som vid ett fast index i matrisen dimension.

## <a name="run-the-job"></a>Kör jobbet
Du kan välja ett datum i det förflutna att starta jobbet från. 

![Markera jobbet och klicka på frågan. Klistra in exemplet nedan.](./media/app-insights-export-stream-analytics/190.png)

Vänta tills jobbet körs.

## <a name="see-results-in-power-bi"></a>Visa resultatet i Power BI
> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](app-insights-export-power-bi.md). Sökvägen som visas här är bara ett exempel som illustrerar hur du bearbetar exporterade data.
> 
> 

Öppna Power BI med ditt arbets eller skolkonto och välj dataset och tabellen som du definierade som utdata för Stream Analytics-jobb.

![Välj din datauppsättning och fält i Power BI.](./media/app-insights-export-stream-analytics/200.png)

Nu kan du använda den här datauppsättningen i rapporter och instrumentpaneler i [Power BI](https://powerbi.microsoft.com).

![Välj din datauppsättning och fält i Power BI.](./media/app-insights-export-stream-analytics/210.png)

## <a name="no-data"></a>Ser du inga data?
* Kontrollera att du [Ange formatet för datum](#set-path-prefix-pattern) korrekt till åååå-MM-DD (med bindestreck).

## <a name="video"></a>Video
Noam Ben Zeev visar hur du bearbetar exporterade data med hjälp av Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Löpande export](app-insights-export-telemetry.md)
* [Detaljerad datamodell referens för egenskapstyper och värden.](app-insights-export-data-model.md)
* [Application Insights](app-insights-overview.md)

