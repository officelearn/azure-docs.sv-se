---
title: "Elastisk frågan begrepp med Azure SQL Data Warehouse | Microsoft Docs"
description: "Elastisk frågan begrepp med Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 295cc59fdb23105534b4e7431902eaa720643330
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Hur du använder elastisk fråga med SQL Data Warehouse



Elastisk frågan med Azure SQL Data Warehouse kan du skriva Transact-SQL i en SQL-databas som skickas via en fjärranslutning till en Azure SQL Data Warehouse-instans genom att använda externa tabeller. Med den här funktionen ger lägre kostnader och mer performant arkitekturer, beroende på scenario.

Den här funktionen kan två primära scenarier:

1. Domänisolering
2. Frågekörningen för fjärråtkomst

### <a name="domain-isolation"></a>Domänisolering

Domänisolering refererar till klassiska data mart-scenariot. I vissa fall, kan en vill tillhandahålla en logisk domän av data till underordnade användare som är isolerat från resten av datalagret för många olika orsaker, inklusive men inte begränsat till:

1. Resursisolering - SQL-databas är optimerad för att hantera ett stort basen för samtidiga användare som betjänar något annorlunda arbetsbelastningar än stora analytiska frågor som datalagret är reserverad för. Isolering säkerställer rätt arbetsbelastningar hanteras av rätt verktyg.
2. Säkerhetsisolering - att dela en auktoriserad data delmängd selektivt via vissa scheman.
3. Begränsade - ange en exempeldata som ”playground” att utforska produktion frågor osv.

Elastisk frågan kan ge möjlighet att enkelt välja delmängder av SQL data warehouse-data och flytta den till en SQL-databasinstans. Dessutom utesluter inte denna isolering kan du också aktivera fjärråtkomst Frågekörningen möjliggör mer intressant ”cache”-scenarier.

### <a name="remote-query-execution"></a>Frågekörningen för fjärråtkomst

Elastisk fråga tillåter fjärråtkomst frågeexekvering på en instans av SQL data warehouse. En kan använda bäst för både SQL database och SQL data warehouse genom att avgränsa varm eller kall data mellan de två databaserna. Användare kan behålla nyare data i en SQL-databas, som kan hantera rapporter och stora mängder genomsnittlig företagsanvändare. När mer data eller beräkning krävs kan en användare avlasta delen av frågan till en SQL data warehouse-instans där stora mängder kan behandlas snabbare och effektivare.



## <a name="elastic-query-overview"></a>Översikt över elastisk fråga

En elastisk fråga kan användas för att tillhandahålla data som finns i en SQL data warehouse instanser av SQL-databasen. Elastisk frågan kan frågor från en SQL-databas som refererar till tabeller i en fjärransluten SQL data warehouse-instans. 

Det första steget är att skapa en extern definitionen av datakällan som refererar till SQL data warehouse instansen, som använder befintliga användarautentiseringsuppgifter inom SQL data warehouse. Det krävs inga ändringar på fjärrinstansen för SQL data warehouse. 

> [!IMPORTANT] 
> 
> Du måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten har behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till fjärrdatakällor.

Därefter skapar vi en fjärransluten externa tabelldefinitionen i en SQL-databasinstans som pekar på en fjärrtabell i SQL data warehouse. När du använder en fråga som använder en extern tabell skickas delen av en fråga som refererar till den externa tabellen till SQL data warehouse-instans som ska bearbetas. När frågan har slutförts skickas resultatet tillbaka till anropande SQL-databasinstansen. Ett kort för att skapa en elastisk fråga mellan SQL-databas och SQL data warehouse, finns det [konfigurera elastisk fråga med SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Mer information om elastisk fråga med SQL-databasen finns på [översikt över Azure SQL Database-elastisk frågan][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Bästa praxis

### <a name="general"></a>Allmänt

- När du använder fjärråtkomst Frågekörningen, se till att du bara välja nödvändiga kolumner och tillämpa rätt filter. Inte bara har Storleksökningen beräkning krävs, men det är också ökar storleken på resultatet och mängden data som behöver därför flyttas mellan de två instanserna.
- Lagra data för analys i både SQL Data Warehouse och SQL-databas i grupperade columnstore för analytiIcal prestanda.
- Se till att partitioneras källtabellerna för frågor och rörelse.
- Se till att SQL-databasinstanser som används som en cache partitioneras om du vill aktivera mer detaljerad uppdateringar och lättare hantering. 
- Använd helst PremiumRS databaser eftersom de ger analytiska fördelarna med grupperade columnstore indexering med fokus på i/o-intensiva arbetsbelastningar med rabatt från Premium-databaser.
- Använda load eller giltighetsdatum identifiering kolumner för upserts i SQL-databas instanser att underhålla-cachekälla integritet efter belastning. 
- Skapa en separat inloggning och användare i din SQL data warehouse-instans för din SQL fjärrinloggning Databasautentiseringsuppgifter definieras i den externa datakällan. 

### <a name="elastic-querying"></a>Elastisk frågor

- Extern tabell och interally cachelagrade tabell finnas som olika objekt med SQL-databasinstansen. Du skapar en vy över den cachelagra delen av den externa tabellerna och vilka unioner både tabeller och tillämpar filter på gräns punkt i varje tabell.

  Anta att vi vill behålla det senaste år av data i en SQL-databasinstansen. Vi har två tabeller **externt Order**, som hänvisar till datalagret sorterar tabeller och **dbo. Order** som motsvarar den senaste år kan du se data i SQL-databasinstansen. I stället för att be användarna att bestämma om du vill fråga en tabell eller en annan skapa vi en vy över båda tabellerna på partitionen kopplingspunkt det senaste året.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  En vy som producerade så vi frågan kompileraren avgöra om den behöver använda data warehouse-instans för att besvara frågan användare. 

  Det finns administration av skickar kompilering, kör och flytta data som är associerade med varje elastisk fråga mot data warehouse-instans. Vara cognizant att varje elastisk fråga räknas av mot concurrency-platser och använder resurser.  


- Om en planer och öka detaljnivån ytterligare i resultatmängden från data warehouse-instansen bör du överväga att materialisering i en tillfällig tabell i SQL-databasen för prestanda och förhindra att onödiga Resursanvändning.

### <a name="moving-data"></a>Flytta data 

- Om möjligt, behålla datahantering enklare och Lägg endast källtabellerna så att uppdateringar är enkelt hanterbar mellan data warehouse och databasen instanser.
- Flytta data på nivån partition med tömning och fyll semantik att minska kostnaden för frågan på nivån data warehouse och mängden data som flyttas till kontinuerligt databasinstansen. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>När du ska välja Azure Analysis Services eller SQL-databas

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- Du tänker använda ditt cacheminne till ett BI-verktyg som överför stora mängder små frågor
- Du behöver subsecond svarstid
- Du har erfarenhet av att hantera/utveckla modeller för Analysis Services 

#### <a name="sql-database"></a>SQL Database

- Du vill fråga Cachedata med SQL
- Du behöver fjärrkörning för vissa frågor
- Du har större cache-krav



## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

F: kan jag använda databaser i en elastisk databaspool med elastisk fråga?

S: Ja. SQL-databaser i en elastisk pool kan använda elastisk frågan. 

F: finns det ett tak för hur många databaser som jag kan använda för elastiska frågan?

S: logiska servrar ha DTU-gränserna för att förhindra kunder från oavsiktliga överskridanden. Om du aktiverar flera databaser för elastiska frågan tillsammans med en instans av SQL Data Warehouse, kan du träffa fästpunkten oväntat. Om detta inträffar kan skicka en begäran om att öka antalet DTU på din logiska server. Du kan öka din kvot genom [skapat ett supportärende](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) och välja *kvot* som typ av begäran

F: kan jag använda raden nivå säkerhet/dynamiska Data maskering med elastisk fråga?

S: kunder som vill använda mer avancerade säkerhetsfunktioner med SQL-databas kan göra det genom att först flytta och lagra data i SQL-databasen. Det går för närvarande använda säkerhet på radnivå eller DDM på data som efterfrågas via externa tabeller. 

F: kan jag skriva från min SQL-databasinstans till informationslagerinstansen?

S: för närvarande stöds inte den här funktionen. Besök vår [Feedback sidan] [ Feedback page] att skapa/rösta för den här funktionen om det här är en funktion som du skulle vilja se i framtiden. 

F: kan jag använda spatialtyper som geometri/geografisk plats?

S: du kan lagra spatialtyper i SQL Data Warehouse som varbinary(max)-värden. När du frågar dessa kolumner med elastisk fråga kan du konvertera dem till lämpliga typer vid körning.

![spatialtyper](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->