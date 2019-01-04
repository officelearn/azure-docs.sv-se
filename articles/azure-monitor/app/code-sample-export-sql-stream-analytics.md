---
title: Exportera till SQL från Azure Application Insights | Microsoft Docs
description: Exportera kontinuerligt Application Insights-data till SQL med Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: 8ed645066eef93ae02722858381af77854d7d1eb
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2019
ms.locfileid: "54001897"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Steg-för-steg-beskrivning: Exportera till SQL från Application Insights med hjälp av Stream Analytics
Den här artikeln visar hur du flyttar dina telemetridata från [Azure Application Insights] [ start] till en Azure SQL-databas med hjälp av [löpande Export] [ export] och [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Löpande export flyttar telemetridata till Azure Storage i JSON-format. Vi parsa JSON-objekt med hjälp av Azure Stream Analytics och skapa rader i en databastabell.

(Mer allmänt löpande Export är sättet att göra analyser om telemetri som dina appar skicka till Application Insights. Du kan anpassa det här kodexemplet för att göra andra saker med den exporterade telemetrin, till exempel aggregering av data.)

Vi börjar med antagandet att du redan har appen som du vill övervaka.

I det här exemplet kommer vi att använda sidan Visa data, men samma mönster kan enkelt utökas till andra datatyper, till exempel anpassade händelser och undantag. 

## <a name="add-application-insights-to-your-application"></a>Lägg till Application Insights i ditt program
Så här kommer du igång:

1. [Konfigurera Application Insights för dina webbsidor](../../azure-monitor/app/javascript.md). 
   
    (I det här exemplet vi att fokusera på att bearbeta data om sidvisningar från klientwebbläsarna, men du kan också ställa in Application Insights för serversidan i din [Java](../../azure-monitor/app/java-get-started.md) eller [ASP.NET](../../azure-monitor/app/asp-net.md) app och bearbeta begäran beroende och annan telemetri.)
2. Publicera din app och titta på dessa data visas i Application Insights-resursen.

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Löpande export alltid matar ut data till ett Azure Storage-konto, så du måste först skapa lagringen.

1. Skapa ett lagringskonto i din prenumeration på den [Azure-portalen][portal].
   
    ![Välj ny, Data, lagring i Azure-portalen. Välj klassisk, väljer du skapa. Ange ett namn för lagring.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Skapa en container
   
    ![I det nya lagringsutrymmet Välj behållare, klicka på panelen för behållare och sedan på Lägg till](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiera lagringsåtkomstnyckeln
   
    Du behöver den snart för att ställa in indata till stream analytics-tjänsten.
   
    ![Öppna inställningar, nycklar, i lagringen, och ta en kopia av den primära åtkomstnyckeln](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta löpande export till Azure storage
1. Bläddra till Application Insights-resurs som du har skapat för ditt program i Azure-portalen.
   
    ![Välj Bläddra, Application Insights, ditt program](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Skapa en löpande export.
   
    ![Välj Lägg till inställningar, löpande Export](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Välj det lagringskonto du skapade tidigare:

    ![Ange mål för export](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Ange händelsetyper som du vill se:

    ![Välj händelsetyper](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Kan vissa data ackumuleras. Luta dig tillbaka och låta personer som använder programmet ett tag. Telemetri kommer så ser du statistiska diagrammen i [metric explorer](../../azure-monitor/app/metrics-explorer.md) och enskilda händelser i [diagnostiksökning](../../azure-monitor/app/diagnostic-search.md). 
   
    Och dessutom data exporteras till din lagring. 
2. Granska de exporterade data, antingen i portalen – Välj **Bläddra**, Välj ditt lagringskonto och sedan **behållare** - eller i Visual Studio. I Visual Studio väljer **visa / Cloud Explorer**, och öppna Azure / Storage. (Om du inte har alternativet kan du behöva installera Azure SDK: Öppna dialogrutan Nytt projekt och öppna Visual C# / i molnet / hämta Microsoft Azure SDK för .NET.)
   
    ![Öppna i Visual Studio, servrar, Azure, lagring](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anteckna den vanliga delen av sökvägen som härleds från programnyckeln namn och instrumentering. 

Händelser skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Så vill vi läsa av data och filtrera bort de fält som vi vill. Det finns många olika sätt som vi kan göra med data, men vår plan är idag att använda Stream Analytics för att flytta data till en SQL-databas. Som gör det enkelt att köra många olika intressanta frågor.

## <a name="create-an-azure-sql-database"></a>Skapa en Azure SQL Database
Igen från din prenumeration i [Azure-portalen][portal], skapa databasen (och en ny server om du redan har en) som du ska skriva data.

![Ny, Data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Kontrollera att databasservern tillåter åtkomst till Azure-tjänster:

![Bläddra, servrar, din server, inställningar, brandvägg, Tillåt åtkomst till Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Skapa en tabell i Azure SQL DB
Ansluta till databasen som skapades i föregående avsnitt med dina önskade hanteringsverktyg. I den här genomgången ska vi använda [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

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

I det här exemplet använder vi data från sidvisningar. Om du vill se andra tillgängliga data, granska dina JSON-utdata och se den [exportera datamodell](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en instans av Azure Stream Analytics
Från den [Azure-portalen](https://portal.azure.com/), Välj Azure Stream Analytics-tjänsten och skapa ett nytt Stream Analytics-jobb:

![Inställningarna för Stream analytics](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

När det nya projektet har skapats, Välj **gå till resurs**.

![Inställningarna för Stream analytics](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Lägg till en ny indata

![Inställningarna för Stream analytics](./media/code-sample-export-sql-stream-analytics/SA004.png)

Ändra det till indatan hämtas från bloben löpande Export:

![Inställningarna för Stream analytics](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Nu behöver du den primära åtkomstnyckeln från ditt Lagringskonto som du antecknade tidigare. Ange som Lagringskontonyckeln.

#### <a name="set-path-prefix-pattern"></a>Ange prefixmönster för sögväg

**Glöm inte att ange formatet för datum till åååå-MM-DD (med bindestreck).**

Prefixet Sökvägsmönster anger hur Stream Analytics hittar indatafilerna i lagringen. Du måste ange den som motsvarar hur löpande Export lagrar data. Konfigurerar du den så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27` är namnet på Application Insights-resurs **allt i gemener**. 
* `1234...` är instrumenteringsnyckeln för Application Insights-resursen **med bindestreck bort**. 
* `PageViews` är typ av data som vi vill analysera. Tillgängliga typer beror på de filter som du anger i löpande Export. Granska den exporterade data för att visa de tillgängliga typerna och se den [exportera datamodell](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` skrivs ett mönster bokstavligt.

Öppna Essentials på dess översiktssidan för att hämta namnet och iKey av Application Insights-resursen, eller öppna inställningar.

> [!TIP]
> Funktionen exemplet används för att kontrollera att du har angett korrekt indatasökvägen. Om det misslyckas: Kontrollera att det finns data i lagring för exemplet tidsintervall som du har valt. Redigera definitionen för indata och kontrollera du ange storage-konto, sökvägsprefix och datumformat korrekt.
> 
> 
## <a name="set-query"></a>Ange fråga
Öppna avsnittet fråga:

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

Observera att de första egenskaperna är specifika för data om sidvisningar. Export av andra typer av telemetri har olika egenskaper. Se den [detaljerad referens för datamodell för egenskapstyper och värden.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Konfigurera utdata till databasen
Välj SQL som utdata.

![Välj utdata i stream analytics](./media/code-sample-export-sql-stream-analytics/SA006.png)

Ange SQL-databas.

![Fyll i informationen om din databas](./media/code-sample-export-sql-stream-analytics/SA007.png)

Stäng guiden och vänta tills ett meddelande om att utdata har ställts in.

## <a name="start-processing"></a>Starta bearbetning
Starta jobbet från Åtgärdsfältet:

![Klicka på Start i stream analytics](./media/code-sample-export-sql-stream-analytics/SA008.png)

Du kan välja om du vill starta bearbetning av data från och med nu eller börja med tidigare data. Den andra är användbar om du har haft löpande Export redan körs på ett tag.

Gå tillbaka till SQL Server Management Tools och titta på de data som flödar i efter ett par minuter. Till exempel använda en fråga så här:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Relaterade artiklar
* [Exportera till Power BI med hjälp av Stream Analytics](../../application-insights/app-insights-export-power-bi.md)
* [Detaljerade data modell-referens för egenskapstyper och värden.](../../azure-monitor/app/export-data-model.md)
* [Löpande Export i Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../application-insights/app-insights-overview.md

