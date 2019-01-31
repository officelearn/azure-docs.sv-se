---
title: Elastisk fråga - åtkomst till data i Azure SQL Data Warehouse från Azure SQL Database | Microsoft Docs
description: Läs om bästa praxis för att använda Elastic Query få åtkomst till data i Azure SQL Data Warehouse från Azure SQL Database.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/11/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 4a45d00559a84c178ab760acf8616f97ce7bb57c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466293"
---
# <a name="best-practices-for-using-elastic-query-in-azure-sql-database-to-access-data-in-azure-sql-data-warehouse"></a>Metodtips för elastisk fråga i Azure SQL Database att komma åt data i Azure SQL Data Warehouse
Läs om bästa praxis för att använda Elastic Query få åtkomst till data i Azure SQL Data Warehouse från Azure SQL Database. 

## <a name="what-is-an-elastic-query"></a>Vad är en elastisk förfrågan?
En elastisk förfrågan kan du använda T-SQL och externa tabeller för att skriva en fråga i en Azure SQL-databas som skickas via en fjärranslutning till ett Azure SQL data warehouse. Med den här funktionen ger kostnadsbesparingar och mer effektivt arkitekturer, beroende på scenario.

Den här funktionen kan två huvudsakliga scenarier:

1. Domänisolering
2. Remote frågekörning

### <a name="domain-isolation"></a>Domänisolering

Domänisolering refererar till det klassiska data mart-scenariot. I vissa fall, kan en vill tillhandahålla en logisk domän av data till underordnade användare som är isolerade från resten av datalagret för många olika skäl, inklusive men inte begränsat till:

1. Resursisolering - SQL-databas är optimerad för att leverera en stor basen för samtidiga användare som betjänar något annorlunda arbetsbelastningar än stora analytiska frågor som datalagret är reserverat för. Isolering säkerställer rätt arbetsbelastningar betjänas av rätt verktyg.
2. Säkerhetsisolering – att dela en auktoriserad data delmängd selektivt via vissa scheman.
3. Begränsade - ange en exempeldata som en ”playground” att utforska produktion frågor osv.

Elastisk fråga kan ge möjlighet att enkelt välja delmängder av SQL data warehouse-data och flytta den till en SQL-databasinstans. Dessutom utesluter inte denna isolering kan du också aktivera Remote frågekörning, vilket ger mer intressant ”cache”-scenarier.

### <a name="remote-query-execution"></a>Remote frågekörning

Elastisk fråga kan fjärranslutna Frågekörningen på en instans av SQL data warehouse. En kan använda bäst av både SQL database och SQL data warehouse genom att ange dina heta och kalla data mellan de två databaserna. Användare kan behålla nyare data i en SQL-databas som kan fungera rapporter och stora mängder genomsnittlig företagsanvändare. När mer data eller beräkning som behövs kan kan en användare avlasta delen av frågan till en SQL data warehouse-instans där stora mängder kan behandlas snabbare och mer effektivt.

## <a name="elastic-query-process"></a>Elastisk fråga i processen
En elastisk förfrågan kan användas för att göra data som finns i en SQL data warehouse tillgänglig för SQL database-instanser. Elastisk fråga kan frågor från en SQL-databas som refererar till tabeller i en fjärransluten SQL data warehouse-instans. 

Det första steget är att skapa en extern definition av datakällan som refererar till SQL data warehouse instansen, som använder befintliga användarautentiseringsuppgifter inom SQL data warehouse. Det krävs inga ändringar på fjärrinstansen för SQL data warehouse. 

> [!IMPORTANT] 
> 
> Du måste ha behörigheten ALTER ANY extern DATAKÄLLA. Den här behörigheten ingår behörigheten ALTER DATABASE. ALTER ANY extern DATAKÄLLA behörighet att referera till fjärrdatakällor.

Skapa sedan en fjärransluten externa tabelldefinition i en SQL-databasinstans som pekar mot en fjärrtabell i SQL data warehouse. När en fråga använder en extern tabell, skickas delen av frågan refererar till den externa tabellen till SQL data warehouse-instans som ska bearbetas. När frågan har slutförts skickas resultatet tillbaka till den anropande SQL database-instansen. Se en kort självstudie för att skapa en elastisk förfrågan mellan SQL database och SQL data warehouse den [konfigurera elastisk fråga med SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Mer information om elastisk fråga med SQL database finns i den [översikt över Azure SQL Database-elastisk fråga][Azure SQL Database elastic query overview].

## <a name="best-practices"></a>Bästa praxis
Använd dessa bästa metoder för att använda elastic query effektivt.

### <a name="general"></a>Allmänt

- När du använder fjärranslutna frågekörning, se till att du bara välja nödvändiga kolumner och tillämpa rätt filter. Inte bara tillåter denna ökning beräkningarna som behövs, men det också ökar storleken på resultatuppsättningen och därför mängden data som behöver flyttas mellan de två instanserna.
- Lagra data för analys i både SQL Data Warehouse och SQL Database i klustrade columnstore för analytiska prestanda.
- Se till att källtabellerna partitioneras för frågor och förflyttning.
- Se till att SQL database-instanser används som ett cacheminne partitioneras för att möjliggöra mer detaljerade uppdateringar och enklare hantering. 
- Använd helst PremiumRS databaserna eftersom de ger analytiska fördelarna med grupperade indexering med fokus på i/o-intensiva arbetsbelastningar till ett rabatterat pris från Premium-databaser.
- När du läser in använda belastning eller ikraftträdandedatum identifiering kolumner för upsertar i SQL Database-instanser för att upprätthålla integriteten för cache-källa. 
- Skapa en separat inloggning och användare i din SQL data warehouse-instans för din SQL database fjärrinloggning autentiseringsuppgifter som definierats i den externa datakällan. 

### <a name="elastic-querying"></a>Elastisk fråga

- I många fall kan en vill hantera en typ av sträckta tabellen, där en del av din tabell är inom SQL-databas som cachelagrade data för prestanda med resten av de data som lagras i SQL Data Warehouse. Du behöver två objekt i SQL Database: en extern tabell i SQL-databas som refererar till bastabellen i SQL Data Warehouse och ”cache-delen av tabellen i SQL-databasen. Överväg att skapa en vy över den cachelagra delen av tabellen och den externa tabellen vilka unioner både tabeller och tillämpar filter som avgränsar data materialiserad i SQL Database och SQL Data Warehouse-data som exponeras via externa tabeller.

  Anta att du vill behålla det senaste året av data i en SQL-databasinstans. Den **externt Order** tabellreferenser datalagret beställningar tabeller. Den **dbo. Order** representerar de senaste åren värdet av data i SQL database-instans. I stället för att be användarna att bestämma om du vill fråga en tabell eller den andra, skapar du en vy över båda tabellerna på det partition datum då det senaste året.

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

  En vy som producerats så vi frågan kompilatorn avgöra om den måste använda informationslagerinstansen för att besvara frågan användare. 

  Det finns bekymra dig om skickar, kompilera, köra och flytta data som är associerade med varje elastisk fråga mot data warehouse-instans. Vara cognizant att varje elastisk fråga räknas mot din samtidighetsfack och använder resurser.  


- Om en planer och öka detaljnivån ytterligare i resultatmängden från informationslagerinstansen bör du överväga att materialisering i en temporär tabell i SQL-databasen för prestanda och för att förhindra att onödiga Resursanvändning.

### <a name="moving-data"></a>Flytta data 

- Om det är möjligt, behålla datahantering enklare med Lägg endast källtabellerna så att uppdateringar är enkelt underhålla mellan instanserna för data warehouse och databasen.
- Flytta data på partitionen-nivå med tömma och fyllning semantik för att minimera kostnaderna fråga på data warehouse-nivå och mängden data som flyttas till att hålla databasinstansen uppdaterade. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>När du ska välja Azure Analysis Services eller SQL-databas

Använda Azure Analysis Services när:

- Du tänker använda din cache med en BI-verktyg som skickar stora mängder små frågor
- Du behöver subsecond svarstid
- Du har erfarenhet av att hantera/utveckla modeller för Analysis Services 

Använda Azure SQL-databas när:

- Du vill fråga cachelagrade data med SQL
- Du behöver fjärrkörning för vissa frågor
- Du har större cache-krav

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

F: Kan jag använda databaser inom en elastisk pool med elastisk fråga?

S: Ja. SQL-databaser inom en elastisk pool kan använda elastisk fråga. 

F: Finns det en gräns för hur många databaser som jag kan använda för elastisk fråga?

S: Det finns inga hård gräns på hur många databaser kan användas för elastisk fråga. Men tillgodoräkna varje elastisk förfrågan (frågor som når SQL Data Warehouse) dig normala samtidighetsgränser.

F: Finns det DTU-gränserna med elastisk fråga?

S: DTU-gränserna är inte införts något annorlunda med elastisk fråga. Standard principen är så att logiska servrar har DTU-gränserna för att förhindra kunder från oavsiktliga höga kostnader. Om du aktiverar flera databaser för elastisk fråga tillsammans med en instans av SQL Data Warehouse, kan du nått gränsen oväntat. Om detta inträffar kan du ansöka om att öka DTU-gränsen för din logiska server. Du kan öka din kvot genom [skapar en supportbegäran](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) och välja *kvot* som typ av begäran

F: Kan jag använda raden på security/Dynamic Data Masking med elastisk fråga?

S: Kunder som vill använda mer avancerade säkerhetsfunktioner med SQL Database kan göra det genom att först flytta och lagra data i SQL-databasen. Du kan inte användas säkerhet på radnivå eller DDM på data som efterfrågas via externa tabeller. 

F: Kan jag skriva från SQL-databasinstans till data warehouse-instans?

S: Den här funktionen stöds för närvarande inte. Besök vår [feedbacksidan] [ Feedback page] att skapa/rösta för den här funktionen om det här är en funktion som du skulle vilja se i framtiden. 

F: Kan jag använda spatial typer som geometry/geografisk plats?

S: Du kan lagra spatial typer i SQL Data Warehouse som varbinary(max)-värden. När du frågar dessa kolumner med hjälp av elastisk fråga kan du konvertera dem till lämplig typer vid körning.

![spatial typer](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)

<!--Article references-->

[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Configure Elastic Query with SQL Data Warehouse]: tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md


