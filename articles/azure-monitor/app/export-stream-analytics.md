---
title: Exportera med Stream Analytics från Azure Application Insights | Microsoft-dokument
description: Stream Analytics kan kontinuerligt omvandla, filtrera och dirigera data som du exporterar från Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 15d1efa3a632024429d41f27fc23c569cd85bec2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536887"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Använda Stream Analytics för att bearbeta exporterade data från Application Insights
[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) är det perfekta verktyget för bearbetning av data [som exporteras från Application Insights](export-telemetry.md). Stream Analytics kan hämta data från en mängd olika källor. Det kan omvandla och filtrera data och sedan dirigera dem till en mängd olika sänkor.

I det här exemplet skapar vi en adapter som tar data från Application Insights, byter namn på och bearbetar vissa fält och leder in dem i Power BI.

> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](../../azure-monitor/app/export-power-bi.md ). Den väg som illustreras här är bara ett exempel för att illustrera hur man bearbetar exporterade data.
> 
> 

![Blockdiagram för export via SA till PBI](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Kontinuerlig export matar alltid ut data till ett Azure Storage-konto, så du måste skapa lagringen först.

1. Skapa ett "klassiskt" lagringskonto i din prenumeration i [Azure-portalen](https://portal.azure.com).
   
   ![I Azure-portalen väljer du Ny, Data, Lagring](./media/export-stream-analytics/030.png)
2. Skapa en container
   
    ![I det nya lagringsutrymmet väljer du Behållare, klickar på panelen Behållare och sedan lägger till](./media/export-stream-analytics/040.png)
3. Kopiera åtkomstnyckeln för lagring
   
    Du behöver det snart för att ställa in indata till stream analytics-tjänsten.
   
    ![Öppna Inställningar, nycklar och ta en kopia av primär åtkomstnyckeln i lagringsutrymmet](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta kontinuerlig export till Azure-lagring
[Kontinuerlig export](export-telemetry.md) flyttar data från Application Insights till Azure-lagring.

1. I Azure-portalen bläddrar du till den Application Insights-resurs som du skapade för ditt program.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/export-stream-analytics/050.png)
2. Skapa en kontinuerlig export.
   
    ![Välj Inställningar, Kontinuerlig export, Lägg till](./media/export-stream-analytics/060.png)

    Välj det lagringskonto som du skapade tidigare:

    ![Ange exportmål](./media/export-stream-analytics/070.png)

    Ange de händelsetyper som du vill visa:

    ![Välj händelsetyper](./media/export-stream-analytics/080.png)

1. Låt vissa data ackumuleras. Luta dig tillbaka och låt andra använda ditt program ett tag. Telemetri kommer in och du ser statistiska diagram i [metriska explorer](../../azure-monitor/platform/metrics-charts.md) och enskilda händelser i [diagnostisk sökning](../../azure-monitor/app/diagnostic-search.md). 
   
    Och även, data kommer att exportera till din lagring. 
2. Kontrollera de exporterade data. I Visual Studio väljer du **Visa /Molnutforskaren**och öppnar Azure/Storage. (Om du inte har det här menyalternativet måste du installera Azure SDK: Öppna dialogrutan Nytt projekt och öppna Visual C# / Cloud / Get Microsoft Azure SDK för .NET.)
   
    ![](./media/export-stream-analytics/04-data.png)
   
    Anteckna den gemensamma delen av sökvägens namn, som härleds från programnamn och instrumenteringsnyckel. 

Händelserna skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Så vi vill läsa händelsedata och filtrera bort de fält vi vill ha. Det finns alla möjliga saker vi kan göra med data, men vårt plan idag är att använda Stream Analytics för att leda data till Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans
På [Azure-portalen](https://portal.azure.com/)väljer du Azure Stream Analytics-tjänsten och skapar ett nytt Stream Analytics-jobb:

![](./media/export-stream-analytics/SA001.png)

![](./media/export-stream-analytics/SA002.png)

När det nya jobbet skapas väljer du **Gå till resurs**.

![](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Lägga till en ny inmatning

![](./media/export-stream-analytics/SA004.png)

Ställ in den så att du tar indata från blobben Kontinuerlig export:

![](./media/export-stream-analytics/SA0005.png)

Nu behöver du primär åtkomstnyckeln från ditt lagringskonto, som du noterade tidigare. Ange detta som lagringskontonyckel.

### <a name="set-path-prefix-pattern"></a>Ange mönster för banprefix

**Var noga med att ställa in datumformatet på YYYY-MM-DD (med streck).**

Sökvägsprefixmönstret anger var Stream Analytics hittar indatafilerna i lagringen. Du måste ställa in den så att den motsvarar hur kontinuerlig export lagrar data. Ställ in det så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27`är namnet på application insights-resursen **alla gemener**.
* `1234...`är instrumenteringsnyckeln för application insights-resursen, **utelämnar streck**. 
* `PageViews`är den typ av data som du vill analysera. Vilka typer som är tillgängliga beror på vilket filter du anger i Kontinuerlig export. Undersök de exporterade data för att se andra tillgängliga typer och se [exportdatamodellen](export-data-model.md).
* `/{date}/{time}`är ett mönster skrivet bokstavligen.

> [!NOTE]
> Kontrollera lagringen för att se till att du får rätt sökväg.
> 

## <a name="add-new-output"></a>Lägg till ny utdata
Välj nu ditt jobb > **Utdata** > **Lägg till**.

![](./media/export-stream-analytics/SA006.png)


![Markera den nya kanalen, klicka på Utdata, Lägg till, Power BI](./media/export-stream-analytics/SA010.png)

Ange ditt **arbets- eller skolkonto** för att auktorisera Stream Analytics för att komma åt din Power BI-resurs. Uppfinna sedan ett namn för utdata och för måldatauppsättningen och tabellen Power BI.

## <a name="set-the-query"></a>Ange frågan
Frågan styr översättningen från indata till utdata.

Använd funktionen Testa för att kontrollera att du får rätt utdata. Ge den exempeldata som du tog från indatasidan. 

### <a name="query-to-display-counts-of-events"></a>Fråga för att visa antal händelser
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

* export-input är det alias vi gav till strömmen indata
* pbi-output är utdataalias vi definierade
* Vi använder [YTTRE APPLY GetElements](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) eftersom händelsenamnet finns i en kapslad JSON-matris. Välj sedan händelsenamnet tillsammans med antalet instanser med det namnet under tidsperioden. Grupp [Group By](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) för-satsen grupperar elementen i tidsperioder på en minut.

### <a name="query-to-display-metric-values"></a>Fråga om du vill visa måttvärden
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

* Den här frågan borrar i måtttelemetrin för att få händelsetiden och måttvärdet. Måttvärdena finns i en matris, så vi använder mönstret YTTRE APPLY GetElements för att extrahera raderna. "myMetric" är namnet på måttet i det här fallet. 

### <a name="query-to-include-values-of-dimension-properties"></a>Fråga om att inkludera värden för dimensionsegenskaper
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

* Den här frågan innehåller värden för dimensionsegenskaperna utan att en viss dimension är vid ett fast index i dimensionsmatrisen.

## <a name="run-the-job"></a>Kör jobbet
Du kan välja ett datum tidigare för att starta jobbet från. 

![Markera jobbet och klicka på Fråga. Klistra in provet nedan.](./media/export-stream-analytics/SA008.png)

Vänta tills jobbet är igång.

## <a name="see-results-in-power-bi"></a>Se resultat i Power BI
> [!WARNING]
> Det finns mycket bättre och enklare [rekommenderade sätt att visa Application Insights-data i Power BI](../../azure-monitor/app/export-power-bi.md ). Den väg som illustreras här är bara ett exempel för att illustrera hur man bearbetar exporterade data.
> 
> 

Öppna Power BI med ditt arbets- eller skolkonto och välj den datauppsättning och tabell som du definierade som utdata för Stream Analytics-jobbet.

![I Power BI väljer du datauppsättning och fält.](./media/export-stream-analytics/200.png)

Nu kan du använda den här datauppsättningen i rapporter och instrumentpaneler i [Power BI](https://powerbi.microsoft.com).

![I Power BI väljer du datauppsättning och fält.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Ser du inga data?
* Kontrollera att du [ställer in datumformatet](#set-path-prefix-pattern) korrekt på YYY-MM-DD (med streck).

## <a name="video"></a>Video
Noam Ben Zeev visar hur du bearbetar exporterade data med Hjälp av Stream Analytics.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Export-to-Power-BI-from-Application-Insights/player]
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Löpande export](export-telemetry.md)
* [Detaljerad datamodellreferens för egenskapstyper och värden.](export-data-model.md)
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)

