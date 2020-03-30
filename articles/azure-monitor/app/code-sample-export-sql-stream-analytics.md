---
title: Exportera till SQL från Azure Application Insights | Microsoft-dokument
description: Exportera kontinuerligt Application Insights-data till SQL med Hjälp av Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 3ef0420cdab64f11b699fd4031ed2b0134f18609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663699"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Genomgång: Exportera till SQL från application insights med Hjälp av Stream Analytics
Den här artikeln visar hur du flyttar dina telemetridata från [Azure Application Insights][start] till en Azure SQL-databas med hjälp av [Kontinuerlig export][export] och Azure [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Kontinuerlig export flyttar dina telemetridata till Azure Storage i JSON-format. Vi tolkar JSON-objekten med Azure Stream Analytics och skapar rader i en databastabell.

(Mer allmänt är kontinuerlig export sättet att göra din egen analys av den telemetri som dina appar skickar till Application Insights. Du kan anpassa det här kodexemplet så att det gör andra saker med den exporterade telemetrin, till exempel aggregering av data.)

Vi börjar med antagandet att du redan har den app du vill övervaka.

I det här exemplet använder vi sidvisningsdata, men samma mönster kan enkelt utökas till andra datatyper, till exempel anpassade händelser och undantag. 

## <a name="add-application-insights-to-your-application"></a>Lägga till programinsikter i ditt program
Så här kommer du igång:

1. [Konfigurera programstatistik för webbsidorna](../../azure-monitor/app/javascript.md). 
   
    (I det här exemplet fokuserar vi på att bearbeta sidvisningsdata från klientwebbläsarena, men du kan också ställa in Application Insights för serversidan av din [Java-](../../azure-monitor/app/java-get-started.md) eller [ASP.NET-app-](../../azure-monitor/app/asp-net.md) och processbegäran, beroende och annan servertelemetri.)
2. Publicera appen och titta på telemetridata som visas i application insights-resursen.

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Kontinuerlig export matar alltid ut data till ett Azure Storage-konto, så du måste skapa lagringen först.

1. Skapa ett lagringskonto i din prenumeration i [Azure-portalen][portal].
   
    ![I Azure-portalen väljer du Ny, Data, Lagring. Välj Klassisk, välj Skapa. Ange ett lagringsnamn.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Skapa en container
   
    ![I det nya lagringsutrymmet väljer du Behållare, klickar på panelen Behållare och sedan lägger till](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiera åtkomstnyckeln för lagring
   
    Du behöver det snart för att ställa in indata till stream analytics-tjänsten.
   
    ![Öppna Inställningar, nycklar och ta en kopia av primär åtkomstnyckeln i lagringsutrymmet](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta kontinuerlig export till Azure-lagring
1. I Azure-portalen bläddrar du till den Application Insights-resurs som du skapade för ditt program.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Skapa en kontinuerlig export.
   
    ![Välj Inställningar, Kontinuerlig export, Lägg till](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Välj det lagringskonto som du skapade tidigare:

    ![Ange exportmål](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ange de händelsetyper som du vill visa:

    ![Välj händelsetyper](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Låt vissa data ackumuleras. Luta dig tillbaka och låt andra använda ditt program ett tag. Telemetri kommer in och du ser statistiska diagram i [metriska explorer](../../azure-monitor/app/metrics-explorer.md) och enskilda händelser i [diagnostisk sökning](../../azure-monitor/app/diagnostic-search.md). 
   
    Och även, data kommer att exportera till din lagring. 
2. Kontrollera de exporterade data, antingen i portalen - välj **Bläddra,** välj ditt lagringskonto och sedan **Behållare** - eller i Visual Studio. I Visual Studio väljer du **Visa /Molnutforskaren**och öppnar Azure/Storage. (Om du inte har det här menyalternativet måste du installera Azure SDK: Öppna dialogrutan Nytt projekt och öppna Visual C# / Cloud / Get Microsoft Azure SDK för .NET.)
   
    ![Öppna Serverbläddraren, Azure, Storage i Visual Studio](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anteckna den gemensamma delen av sökvägens namn, som härleds från programnamn och instrumenteringsnyckel. 

Händelserna skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Så vi vill läsa händelsedata och filtrera bort de fält vi vill ha. Det finns alla möjliga saker vi kan göra med data, men vår plan idag är att använda Stream Analytics för att flytta data till en SQL-databas. Det kommer att göra det enkelt att köra massor av intressanta frågor.

## <a name="create-an-azure-sql-database"></a>Skapa en Azure SQL Database
Återigen från din prenumeration i [Azure Portal][portal], skapa databasen (och en ny server, om du inte redan har en) som du ska skriva data.

![Nytt, Data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Kontrollera att databasservern tillåter åtkomst till Azure-tjänster:

![Bläddra, Servrar, din server, Inställningar, Brandvägg, Tillåt åtkomst till Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Skapa en tabell i Azure SQL DB
Anslut till databasen som skapats i föregående avsnitt med det verktyg för hantering som du föredrar. I den här genomgången kommer vi att använda [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Skapa en ny fråga och kör följande T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

I det här exemplet använder vi data från sidvisningar. Om du vill visa andra tillgängliga data bör du kontrollera JSON-utdata och se [exportdatamodellen](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans
På [Azure-portalen](https://portal.azure.com/)väljer du Azure Stream Analytics-tjänsten och skapar ett nytt Stream Analytics-jobb:

![Inställningar för strömma analys](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

När det nya jobbet skapas väljer du **Gå till resurs**.

![Inställningar för strömma analys](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Lägga till en ny inmatning

![Inställningar för strömma analys](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ställ in den så att du tar indata från blobben Kontinuerlig export:

![Inställningar för strömma analys](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Nu behöver du primär åtkomstnyckeln från ditt lagringskonto, som du noterade tidigare. Ange detta som lagringskontonyckel.

#### <a name="set-path-prefix-pattern"></a>Ange mönster för banprefix

**Var noga med att ställa in datumformatet på YYYY-MM-DD (med streck).**

Sökvägsprefixmönstret anger hur Stream Analytics hittar indatafilerna i lagringen. Du måste ställa in den så att den motsvarar hur kontinuerlig export lagrar data. Ställ in det så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27`är namnet på application insights-resursen, **allt i gemener**. 
* `1234...`är instrumenteringsnyckeln för application insights-resursen **med streck borttagna**. 
* `PageViews`är den typ av data vi vill analysera. Vilka typer som är tillgängliga beror på vilket filter du anger i Kontinuerlig export. Undersök de exporterade data för att se andra tillgängliga typer och se [exportdatamodellen](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}`är ett mönster skrivet bokstavligen.

Om du vill hämta namnet och iKey för din Application Insights-resurs öppnar du Essentials på översiktssidan eller öppnar Inställningar.

> [!TIP]
> Använd funktionen Exempel för att kontrollera att du har ställt in indatasökvägen korrekt. Om det misslyckas: Kontrollera att det finns data i lagringen för det exempeltidsintervall du valde. Redigera indatadefinitionen och kontrollera att du har angett lagringskontot, sökvägsprefixet och datumformatet korrekt.

 
## <a name="set-query"></a>Ange fråga
Öppna frågeavsnittet:

Ersätt standardfrågan med:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observera att de första egenskaperna är specifika för sidvisningsdata. Export av andra telemetrityper har olika egenskaper. Se den [detaljerade datamodellreferensen för egenskapstyperna och värdena.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurera utdata till databas
Välj SQL som utdata.

![I strömanalys väljer du Utdata](./media/code-sample-export-sql-stream-analytics/SA006.png)

Ange SQL-databasen.

![Fyll i informationen i databasen](./media/code-sample-export-sql-stream-analytics/SA007.png)

Stäng guiden och vänta på ett meddelande om att utdata har ställts in.

## <a name="start-processing"></a>Börja bearbeta
Starta jobbet från åtgärdsfältet:

![Klicka på Start i strömanalys](./media/code-sample-export-sql-stream-analytics/SA008.png)

Du kan välja om du vill börja bearbeta data från och med nu eller börja med tidigare data. Det senare är användbart om du har haft kontinuerlig export redan körs ett tag.

Efter några minuter går du tillbaka till SQL Server Management Tools och tittar på data som flödar in. Använd till exempel en fråga som denna:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Relaterade artiklar
* [Exportera till PowerBI med Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Detaljerad datamodellreferens för egenskapstyper och värden.](../../azure-monitor/app/export-data-model.md)
* [Kontinuerlig export i programinsikter](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

