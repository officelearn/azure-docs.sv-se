---
title: Exportera med hjälp av Stream Analytics från Azure Application Insights | Microsoft Docs
description: Stream Analytics kan kontinuerligt transformera, filtrera och dirigera data som du har exporterat från Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 31594221-17bd-4e5e-9534-950f3b022209
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: mbullwin
ms.openlocfilehash: a6e9df1b5be0565e859c866b6ceb8ef44e6b271a
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812548"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Använd Stream Analytics för att bearbeta exporterade data från Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) är det perfekta verktyget för bearbetning av data [exporterats från Application Insights](export-telemetry.md). Stream Analytics kan hämta data från olika källor. Det kan transformera och filtrera data och sedan dirigera till en mängd olika mottagare.

I det här exemplet skapar vi en adapter som hämtar data från Application Insights, byter namn på och bearbetar vissa fält och kommer till Power BI.

> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](../../application-insights/app-insights-export-power-bi.md). Den här sökvägen är bara ett exempel som illustrerar hur du bearbetar exporterade data.
> 
> 

![Blockdiagram för export via SA att PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Löpande export alltid matar ut data till ett Azure Storage-konto, så du måste först skapa lagringen.

1. Skapa ett ”klassiska” storage-konto i din prenumeration i den [Azure-portalen](https://portal.azure.com).
   
   ![Välj ny, Data, lagring i Azure-portalen](./media/export-stream-analytics/030.png)
2. Skapa en container
   
    ![I det nya lagringsutrymmet Välj behållare, klicka på panelen för behållare och sedan på Lägg till](./media/export-stream-analytics/040.png)
3. Kopiera lagringsåtkomstnyckeln
   
    Du behöver den snart för att ställa in indata till stream analytics-tjänsten.
   
    ![Öppna inställningar, nycklar, i lagringen, och ta en kopia av den primära åtkomstnyckeln](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta löpande export till Azure storage
[Löpande export](export-telemetry.md) flyttar data från Application Insights i Azure storage.

1. Bläddra till Application Insights-resurs som du har skapat för ditt program i Azure-portalen.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/export-stream-analytics/050.png)
2. Skapa en löpande export.
   
    ![Välj Lägg till inställningar, löpande Export](./media/export-stream-analytics/060.png)

    Välj det lagringskonto du skapade tidigare:

    ![Ange mål för export](./media/export-stream-analytics/070.png)

    Ange händelsetyper som du vill se:

    ![Välj händelsetyper](./media/export-stream-analytics/080.png)

1. Kan vissa data ackumuleras. Luta dig tillbaka och låta personer som använder programmet ett tag. Telemetri kommer så ser du statistiska diagrammen i [metric explorer](../../application-insights/app-insights-metrics-explorer.md) och enskilda händelser i [diagnostiksökning](../../azure-monitor/app/diagnostic-search.md). 
   
    Och dessutom data exporteras till din lagring. 
2. Granska de exporterade data. I Visual Studio väljer **visa / Cloud Explorer**, och öppna Azure / Storage. (Om du inte har alternativet kan du behöva installera Azure SDK: Öppna dialogrutan Nytt projekt och öppna Visual C# / i molnet / hämta Microsoft Azure SDK för .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Anteckna den vanliga delen av sökvägen som härleds från programnyckeln namn och instrumentering. 

Händelser skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Så vill vi läsa av data och filtrera bort de fält som vi vill. Det finns många olika sätt som vi kan göra med data, men vår plan är idag att använda Stream Analytics för att skicka data till Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en instans av Azure Stream Analytics
Från den [Azure-portalen](https://portal.azure.com/), Välj Azure Stream Analytics-tjänsten och skapa ett nytt Stream Analytics-jobb:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

När det nya projektet har skapats, Välj **gå till resurs**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Lägg till en ny indata

![](./media/export-stream-analytics/SA004.png)

Ändra det till indatan hämtas från bloben löpande Export:

![](./media/export-stream-analytics/SA0005.png)

Nu behöver du den primära åtkomstnyckeln från ditt Lagringskonto som du antecknade tidigare. Ange som Lagringskontonyckeln.

### <a name="set-path-prefix-pattern"></a>Ange prefixmönster för sögväg

**Glöm inte att ange formatet för datum till åååå-MM-DD (med bindestreck).**

Prefixet Sökvägsmönster anger där Stream Analytics hittar indatafilerna i lagringen. Du måste ange den som motsvarar hur löpande Export lagrar data. Konfigurerar du den så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27` är namnet på Application Insights-resursen **gemener**.
* `1234...` är instrumenteringsnyckeln för Application Insights-resurs **om du utesluter tankstreck**. 
* `PageViews` är typ av data som du vill analysera. Tillgängliga typer beror på de filter som du anger i löpande Export. Granska den exporterade data för att visa de tillgängliga typerna och se den [exportera datamodell](export-data-model.md).
* `/{date}/{time}` skrivs ett mönster bokstavligt.

> [!NOTE]
> Kontrollera lagringen för att få sökvägen rätt.
> 

## <a name="add-new-output"></a>Lägg till nya utdata
Välj nu ditt jobb > **utdata** > **Lägg till**.

![](./media/export-stream-analytics/SA006.png)


![Välj den nya kanalen, klicka på utdata, Lägg till, Power BI](./media/export-stream-analytics/SA010.png)

Ange din **arbets- eller skolkonto** att auktorisera Stream Analytics för att komma åt din Power BI-resurs. Skriv sedan ett namn för utdata och för att målet Power BI-datauppsättning och en tabell.

## <a name="set-the-query"></a>Skapa en fråga
Frågan reglerar översättningen från indata till utdata.

Använd funktionen Test för att kontrollera att du får rätt utdata. Ge den exempeldata som du har gjort på sidan indata. 

### <a name="query-to-display-counts-of-events"></a>Fråga för att visa antalet händelser
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

* export-indata är det alias som vi har gett till en dataström som indata
* pbi-utdata är utdataaliaset som vi definierade
* Vi använder [yttre gäller GetElements](https://msdn.microsoft.com/library/azure/dn706229.aspx) eftersom händelsenamn är i en kapslad JSON-matris. Välj hämtar sedan händelsenamn, tillsammans med en uppräkning av antalet instanser med det namnet under tidsperioden. Den [Group By](https://msdn.microsoft.com/library/azure/dn835023.aspx) satsen grupperar elementen i tidsperioder på en minut.

### <a name="query-to-display-metric-values"></a>Fråga för att visa måttvärden
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

* Den här frågan tester till mått-telemetri för att få tidpunkt för händelsen och mätvärdet. Metrisk värdena är i en matris, så vi använder mönstret yttre gäller GetElements för att extrahera rader. ”myMetric” är namnet på måttet i det här fallet. 

### <a name="query-to-include-values-of-dimension-properties"></a>Query som innehåller värdena för dimensionsegenskaper
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
Du kan välja ett datum tidigare att starta jobbet från. 

![Välj jobbet och klicka på frågan. Klistra in exemplet nedan.](./media/export-stream-analytics/SA008.png)

Vänta tills jobbet körs.

## <a name="see-results-in-power-bi"></a>Visa resultatet i Power BI
> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](../../application-insights/app-insights-export-power-bi.md). Den här sökvägen är bara ett exempel som illustrerar hur du bearbetar exporterade data.
> 
> 

Öppna Power BI med ditt arbets eller skolkonto och välj datauppsättningen och tabellen som du definierade som utdata från Stream Analytics-jobb.

![Välj din datauppsättning och fält i Power BI.](./media/export-stream-analytics/200.png)

Nu kan du använda den här datauppsättningen i rapporter och instrumentpaneler i [Power BI](https://powerbi.microsoft.com).

![Välj din datauppsättning och fält i Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Ser du inga data?
* Kontrollera att du [ange datumformatet](#set-path-prefix-pattern) korrekt till åååå-MM-DD (med bindestreck).

## <a name="video"></a>Video
Noam Ben Zeev visar hur du bearbetar exporterade data med hjälp av Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Löpande export](export-telemetry.md)
* [Detaljerade data modell-referens för egenskapstyper och värden.](export-data-model.md)
* [Application Insights](../../application-insights/app-insights-overview.md)

