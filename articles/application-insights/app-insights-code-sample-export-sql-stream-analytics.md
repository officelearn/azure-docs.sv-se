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
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: e410bb87d017a7659c3eaffbedef378aaf7f5716
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Genomgång: Exportera till SQL från Application Insights med Stream Analytics
Den här artikeln visar hur du flyttar telemetridata från [Azure Application Insights] [ start] till en Azure SQL-databas med hjälp av [löpande Export] [ export] och [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Löpande export flyttar telemetridata till Azure Storage i JSON-format. Vi parsa JSON-objekt med Azure Stream Analytics och skapa rader i en databastabell.

(Mer generellt löpande Export är sätt att göra egna analys av telemetri dina appar skicka till Application Insights. Du kan anpassa det här kodexemplet om du vill göra andra saker med den exporterade telemetrin, till exempel aggregering av data.)

Vi börjar med att anta att du redan har appen som du vill övervaka.

I det här exemplet ska vi använda sidan Visa data, men samma mönster kan enkelt utökas till andra datatyper, till exempel anpassade händelser och undantag. 

## <a name="add-application-insights-to-your-application"></a>Lägg till Application Insights i ditt program
Så här kommer du igång:

1. [Konfigurera Application Insights för webbsidor](app-insights-javascript.md). 
   
    (I det här exemplet ska vi fokusera på bearbetning av data om sidvisningar från klientens webbläsare, men du kan också ställa in Programinsikter för serversidan av din [Java](app-insights-java-get-started.md) eller [ASP.NET](app-insights-asp-net.md) app och bearbeta begäran, beroende och andra server telemetri.)
2. Publicera din app och titta på telemetridata som visas i Application Insights-resurs.

## <a name="create-storage-in-azure"></a>Skapa lagring i Azure
Löpande export alltid matar ut data till ett Azure Storage-konto, så måste du först skapa lagring.

1. Skapa ett lagringskonto i din prenumeration i den [Azure-portalen][portal].
   
    ![Välj ny, Data, lagring i Azure-portalen. Välj klassisk, väljer du skapa. Ange ett namn för lagring.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Skapa en behållare
   
    ![I det nya lagringsutrymmet Välj behållare, klicka på panelen behållare och Lägg till](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. Kopiera lagringsåtkomstnyckel
   
    Du behöver den snart att ställa in indata till stream analytics-tjänsten.
   
    ![I lagringen, öppna inställningarna för nycklar, och ta en kopia av den primära åtkomstnyckeln](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Starta löpande export till Azure-lagring
1. Bläddra till Application Insights-resurs som du har skapat för ditt program i Azure-portalen.
   
    ![Klicka på Bläddra, Application Insights för ditt program](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. Skapa en löpande export.
   
    ![Välj Lägg till inställningar för löpande Export](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Välj lagringskontot som du skapade tidigare:

    ![Ange mål för export](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Ange händelsetyper som du vill visa:

    ![Välj händelsetyper](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Kan vissa data ackumuleras. Sitta tillbaka och låta personer använder programmet ett tag. Telemetri kommer så ser du statistiska diagram i [mått explorer](app-insights-metrics-explorer.md) och enskilda händelser i [diagnostiska Sök](app-insights-diagnostic-search.md). 
   
    Och även exporteras data till din lagring. 
2. Granska de exportera data, antingen i portalen – Välj **Bläddra**, Välj ditt lagringskonto och sedan **behållare** - eller i Visual Studio. I Visual Studio väljer **visa / Cloud Explorer**, och öppna Azure / lagring. (Om du inte har alternativet, måste du installera Azure SDK: öppna dialogrutan Nytt projekt och Visual C# / molnet / hämta Microsoft Azure SDK för .NET.)
   
    ![Öppna i Visual Studio, servrar, Azure, lagring](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Anteckna den vanliga del av sökvägen som härleds från programmet namn och instrumentation nyckeln. 

Händelser skrivs till blob-filer i JSON-format. Varje fil kan innehålla en eller flera händelser. Vi vill så att läsa informationen om händelsen och filtrera bort de fält som vi vill. Det finns många olika sätt som vi kan göra med data, men vår plan är idag att använda Stream Analytics för att flytta data till en SQL-databas. Som gör det lätt att köra många intressanta frågor.

## <a name="create-an-azure-sql-database"></a>Skapa en Azure SQL Database
Igen från din prenumeration i [Azure-portalen][portal], skapa databasen (och en ny server om du har redan ett) där du kan skriva data.

![Nya Data, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Kontrollera att databasservern tillåter åtkomst till Azure-tjänster:

![Bläddra, servrar, din server, inställningar, brandvägg, Tillåt åtkomst till Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Skapa en tabell i Azure SQL DB
Ansluta till databasen som skapats i föregående avsnitt med din önskade hanteringsverktyg. I den här genomgången ska vi använda [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

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

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

I det här exemplet använder data från sidvisningar. Om du vill visa andra tillgängliga data inspektera JSON-utdata och se den [exportera datamodellen](app-insights-export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Skapa en Azure Stream Analytics-instans
Från den [Azure-portalen](https://portal.azure.com/), Välj Azure Stream Analytics-tjänsten och skapa ett nytt Stream Analytics-jobb:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

När det nya jobbet skapas, Välj **finns resurs**.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Lägga till en ny indata

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

Ställ in den att hämta indata från din löpande Export blob:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

Nu behöver du den primära åtkomstnyckeln från ditt Lagringskonto som du antecknade tidigare. Ange som Lagringskontots åtkomstnyckel.

#### <a name="set-path-prefix-pattern"></a>Ange sökvägar för prefix

**Glöm inte att ange datumformatet för åååå-MM-DD (med bindestreck).**

Prefixet sökvägar anger hur Stream Analytics hittar indatafilerna i lagringen. Du måste konfigurera den så att de motsvarar hur löpande Export lagrar data. Ställ in den så här:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

I det här exemplet:

* `webapplication27` är namnet på Application Insights-resurs **i gemen**. 
* `1234...` är instrumentation nyckel för Application Insights-resursen **med bindestreck bort**. 
* `PageViews` är typ av data som vi vill analysera. Tillgängliga typer är beroende av filtret som du anger i löpande Export. Granska exporterade data för att se de tillgängliga typerna och se den [exportera datamodellen](app-insights-export-data-model.md).
* `/{date}/{time}` ett mönster skrivs bokstavligt.

Öppna Essentials på dess översiktssidan för att få namnet och iKey av Application Insights-resurs, eller öppna inställningar.

> [!TIP]
> Funktionen exempel används för att kontrollera att du har angett korrekt inkommande sökvägen. Om det misslyckas: Kontrollera att det finns data i lagringen för det exemplet tidsintervallet som du har valt. Redigera den inkommande definitionen och kontrollerar du ange storage-konto, sökväg prefix och datumformat korrekt.
> 
> 
## <a name="set-query"></a>Ange fråga
Öppna avsnittet fråga:

Ersätt standardfråga med:

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

Observera att de första några egenskaperna är specifika för data om sidvisningar. Export av andra telemetri typer har olika egenskaper. Finns det [detaljerad data model-referens för egenskapstyper och värden.](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Ställ in utdata till databasen
Välj SQL som utdata.

![Välj utdata i stream analytics](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

Ange SQL-databasen.

![Fyll i informationen för din databas](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

Stäng guiden och vänta tills ett meddelande om att utdata har ställts in.

## <a name="start-processing"></a>Starta bearbetning
Starta jobbet från Åtgärdsfältet:

![Klicka på Start i stream analytics](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

Du kan välja om du vill starta bearbetning av data från och med nu, eller starta med tidigare data. Den andra är användbar om du har haft löpande Export redan körs på ett tag.

Gå tillbaka till SQL Server-hanteringsverktyg och titta på de data som flödar i efter några minuter. Till exempel använda en fråga så här:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Relaterade artiklar
* [Exportera till PowerBI med Stream Analytics](app-insights-export-power-bi.md)
* [Detaljerad datamodell referens för egenskapstyper och värden.](app-insights-export-data-model.md)
* [Löpande Export i Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

