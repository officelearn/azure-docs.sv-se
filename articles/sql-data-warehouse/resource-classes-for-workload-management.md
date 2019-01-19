---
title: Resursklasser för hantering av arbetsbelastning – Azure SQL Data Warehouse | Microsoft Docs
description: Riktlinjer för att hantera samtidighet och beräkningsresurser för frågor i Azure SQL Data Warehouse med resursklasser.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/26/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 9ed3ab89387afc78bd631416a683e11f4dc7054a
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402259"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Hantering av arbetsbelastning med resursklasser i Azure SQL Data Warehouse
Riktlinjer för att hantera minne och samtidighet för frågor i Azure SQL Data Warehouse med hjälp av resursklasser.  
 
## <a name="what-is-workload-management"></a>Vad är hantering av arbetsbelastning?
Hantering av arbetsbelastning är möjligheten att optimera prestanda i alla frågor. En väl utformad arbetsbelastning körs frågor och Läs in åtgärder effektivt oavsett om de är beräkningsintensiva eller i/o-intensiva.  SQL Data Warehouse ger funktioner för hantering av arbetsbelastning för miljöer med flera användare. Ett informationslager är inte avsedd för arbetsbelastningar för flera innehavare.

Prestanda-kapaciteten för ett data warehouse bestäms av den [data informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md). 

- Begränsningar för minne och samtidighet för alla profiler för prestanda finns [minne och samtidighet gränser](memory-and-concurrency-limits.md).
- Om du vill justera prestanda kapacitet, kan du [skala upp eller ned](quickstart-scale-compute-portal.md).

Prestanda-kapaciteten för en fråga bestäms av frågans resursklass. I resten av den här artikeln beskrivs vad resursklasser är och hur du ändrar dem.

## <a name="what-are-resource-classes"></a>Vad är resursklasser?
Prestanda-kapaciteten för en fråga bestäms av användarens resursklass.  Resursklasser bestäms förväg resursbegränsningar i Azure SQL Data Warehouse som reglerar beräkningsresurser och samtidighet för frågekörning. Resursklasser hjälper dig att hantera arbetsbelastningen genom att ange begränsningar för antalet frågor som körs samtidigt och vilka beräkningsresurser som tilldelats varje fråga. Det finns en transaktion av mellan minne och samtidighet.

- Mindre resursklasser minska maximalt minne per fråga, men ökar samtidighet.
- Större resursklasser ökar maximalt minne per fråga, men minskar. 

Det finns två typer av klasser:

- Klasser statiska resurser som är anpassade för ökad samtidighet på en datauppsättning storlek som har lösts.
- Dynamiska resursklasser som lämpar sig för datauppsättningar som växer i storlek och ökar prestanda som servicenivån skalas upp.   

Resursklasser använda samtidighetsfack för att mäta användning av databasresurser.  [Samtidighetsfack](#concurrency-slots) beskrivs senare i den här artikeln. 

- Om du vill visa resursanvändningen för resursklasser, se [minne och samtidighet gränser](memory-and-concurrency-limits.md#concurrency-maximums).
- Om du vill justera resursklassen, du kan köra frågan under en annan användare eller [ändra den aktuella användarens resursklass](#change-a-users-resource-class) medlemskap. 

### <a name="static-resource-classes"></a>Statiska resursklasser
Statiska resursklasser allokera samma mängd minne, oavsett den aktuella prestandanivån, som mäts i [data informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md). Eftersom frågor får samma minnesallokering oavsett prestandanivån, [skala ut datalagret](quickstart-scale-compute-portal.md) gör att flera frågor som ska köras i en resursklass.  Statiska resursklasser är perfekt om datavolymen som är känd och konstant.

Statiska resursklasser implementeras med dessa fördefinierade databasroller:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamiska resursklasser
Dynamiska resursklasser allokera en variabel mängd minne beroende på den aktuella servicenivån. Statiska resursklasser är fördelaktigt högre samtidighet och statiska datavolymer, lämpar dynamiska resursklasser sig bättre för en växande eller variabel mängd data.  När du skalar upp till en större servicenivå får dina frågor automatiskt mer minne.  

De dynamiska resursklasser implementeras med dessa fördefinierade databasroller:

- smallrc
- mediumrc
- largerc
- xlargerc 

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Gen2 dynamiska resursklasser är verkligen dynamiska
När gräva i detaljerna för dynamiska resursklasser på Gen1, finns det några uppgifter som lägger till ytterligare komplexitet att förstå deras beteende:

- Klassen smallrc resurser fungerar med en fast minnesmodell som en statisk resursklass.  Smallrc frågor får inte mer minne som servicenivån ökas dynamiskt.
- När servicenivåer ändrar, kan tillgängliga fråga samtidighet gå upp eller ned.
- Skala tjänster nivåer ger inte en proportionell ändring det minne som allokerats till samma resursklasser.

På **Gen2 endast**, dynamiska resursklasser är verkligen dynamiska adresser punkter som nämns ovan.  Den nya regeln är 3-10-22 – 70 för minnesallokeringar i procent för små-medium-stora-xlarge resursklasser **oavsett servicenivå**.  I tabellen nedan har samlad information om procenttal för allokering av minne och det minsta antalet samtidiga förfrågningar som körs, oavsett servicenivån.

| Resursklass | Procentandelen minne | Min samtidiga frågor |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3%                | 32                     |
| mediumrc       | 10 %               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70 %               | 1                      |


### <a name="default-resource-class"></a>Standard resursklass
Som standard varje användaren är medlem i den dynamiska resursklassen **smallrc**. 

Resursklass av tjänstadministratör vara högst smallrc och kan inte ändras.  Tjänstadministratör är användaren som skapades under etableringen.  Tjänstadministratör i den här kontexten är inloggningen som angetts för ”serveradministratören” när skapar en ny SQL Data Warehouse-instans med en ny server.

> [!NOTE]
> Användare eller grupper som har definierats som Active Directory-administratör är också tjänstadministratörer.
>
>

## <a name="resource-class-operations"></a>Resursåtgärder för klass

Resursklasser är utformade för att förbättra prestanda för data management och manipulering av aktiviteter. Komplexa frågor kan också dra från att köras under en stor resursklass. Till exempel fråga prestanda för stora kopplingar och typer kan förbättra när resursklassen är tillräckligt stor för att aktivera frågan som ska köras i minnet.

### <a name="operations-governed-by-resource-classes"></a>Åtgärder som styrs av resursklasser

De här åtgärderna regleras av resursklasser:

* INSERT-VÄLJ, UPPDATERA, TA BORT
* Välj (vid frågor till användartabeller)
* ALTER INDEX - ÅTERSKAPNING eller REORGANIZE
* ALTER TABLE REBUILD
* SKAPA INDEX
* SKAPA KLUSTRADE COLUMNSTORE-INDEX
* SKAPA TABLE AS SELECT (CTAS)
* Läsa in data
* Dataflyttningsåtgärder som utförs av Data Movement Service (DMS)

> [!NOTE]  
> Välj instruktioner på dynamiska hanteringsvyer (DMV) eller andra system vyer inte regleras av någon av samtidighetsgränser. Du kan övervaka systemet, oavsett antalet frågor som körs på den.
> 
> 

### <a name="operations-not-governed-by-resource-classes"></a>Åtgärder som inte styrs av resursklasser
Vissa frågor körs alltid i resursklass smallrc även om användaren är medlem i en större resursklass. Frågorna undantagna räknas inte mot gränsen för samtidighet. Till exempel om samtidighet gränsen är 16, många användare kan vara att välja från systemvyer utan att påverka de tillgängliga samtidighetsfack.

Följande instruktioner är undantagna från resursklasser och körs alltid i smallrc:

* Skapa eller ta bort tabell
* ALTER TABLE... VÄXEL, dela och slå samman partitionen
* ALTER INDEX INAKTIVERA
* TA BORT INDEXET
* Skapa, uppdatera eller DROP STATISTICS
* TRUNKERA TABELLEN
* ALTER AUTHORIZATION
* SKAPA INLOGGNING
* Skapa, ändra eller DROP USER
* Skapa, ändra eller släppa proceduren
* Skapa eller ta bort vy
* INFOGA VÄRDEN
* Välj från systemvyer och DMV: er
* FÖRKLARA
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Samtidighetsfack
Samtidighetsfack är ett praktiskt sätt att spåra resurserna som är tillgängliga för frågekörning. De har liknande biljetter som du köper för att boka platser på en konsert eftersom platserna är begränsade. Det totala antalet samtidighetsfack per datalagret bestäms av servicenivån. Innan en fråga kan börja köra, måste den kunna reservera tillräckligt många samtidighetsfack. När en fråga är klar släpper dess samtidighetsfack.  

- Frågor som körs med 10 samtidighetsfack kan komma åt 5 gånger mer beräkningsresurser än frågor som körs med 2 samtidighetsfack.
- Om varje fråga kräver 10 samtidighetsfack och det finns 40 samtidighetsfack, kan endast 4 frågor köras samtidigt.
 
Endast resursstyrd frågor använder samtidighetsfack. Systemfrågor och vissa trivial frågor förbrukar inte inga fack. Det exakta antalet samtidighetsfack förbrukas bestäms av frågans resursklass.

## <a name="view-the-resource-classes"></a>Visa resursklasser

Resursklasser implementeras som fördefinierade databasroller. Det finns två typer av resursklasser: dynamiska och statiska. Om du vill visa en lista över resursklasser, använder du följande fråga:

```sql
SELECT name 
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Ändra en användares resursklass

Resursklasser implementeras genom att tilldela användare till databasroller. När en användare kör en fråga körs frågan med användarens resursklass. När en användare är medlem i rollen smallrc eller staticrc10, till exempel köras deras frågor med små mängder minne. När en databasanvändare är medlem av databasrollerna xlargerc eller staticrc80, kör deras frågor med stora mängder minne. 

Använd den lagrade proceduren för att öka en användares resursklass [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Om du vill minska resursklassen använda [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Prioritet för resurs-klass
Användare kan vara medlemmar i flera klasser. När en användare tillhör mer än en resursklass:

- Dynamiska resursklasser företräde framför statiska resursklasser. Till exempel om en användare är medlem i både mediumrc(dynamic) och staticrc80 (statiska), köra frågor med mediumrc.
- Större resursklasser företräde framför mindre resursklasser. Till exempel om en användare är medlem i mediumrc och largerc, köra frågor med largerc. Om en användare är medlem i staticrc20 såväl statirc80, på samma sätt kan köras frågor med staticrc80 resursallokeringar.

## <a name="recommendations"></a>Rekommendationer
Vi rekommenderar att du skapar en användare som är dedikerade för att köra en viss typ av fråga eller läsa in åtgärder. Sedan ger användaren en permanent resursklass i stället för att ändra resursklass regelbundet. Med hänsyn till att statiska resursklasser råd större övergripande kontroll på arbetsbelastningen föreslår vi också använda dessa först innan du bestämmer dynamiska resursklasser.

### <a name="resource-classes-for-load-users"></a>Resursklasser för load-användare
`CREATE TABLE` använder klustrade columnstore-index som standard. Genom att komprimera data i ett columnstore index är en minneskrävande åtgärd och minnesbelastning kan minska index kvalitet. Därför är det sannolikt att kräva en högre resursklass vid inläsning av data. För att säkerställa belastningar har tillräckligt med minne, kan du skapa en användare som är avsedda att köra inläsningar och tilldela användaren till en högre resursklass.

Det minne som behövs för att bearbeta belastningar effektivt beror på typen av tabellen som lästs in och storleken på data. Läs mer på minneskrav [maximera radgrupps kvalitet](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

När du har bestämt minneskravet, väljer du om du vill tilldela en statisk eller dynamisk resursklass load-användaren.

- Använd en statisk resursklass när tabellen minneskrav faller inom ett visst intervall. Inläsningar körs den med lämpliga minne. När du skalar datalagret behöver belastningar som inte mer minne. Genom att använda en statisk resursklass, håll minnesallokeringar konstant. Den här konsekvensen frigör du minne och gör att fler frågor körs samtidigt. Vi rekommenderar att nya lösningar använder statiska resursklasser först eftersom de ger större kontroll.
- Använda en dynamisk resursklass när tabellen minneskrav varierar mycket. Inläsningar kan kräva mer minne än den aktuella DWU eller cDWU nivå ger. Därför skalning datalagret lägger till mer minne belastningen åtgärder, vilket gör inläsningar att utföra snabbare.

### <a name="resource-classes-for-queries"></a>Resursklasser för frågor

Vissa frågor är beräkningsintensiva och vissa är inte.  

- Välj en dynamisk resursklass när frågor är komplexa, men behöver inte hög samtidighet.  Till exempel är Generera rapporter för dagliga och veckovisa en tillfällig behovet av resurser. Om rapporterna som bearbetar stora mängder data, ger skala data warehouse mer minne till användarens befintliga resursklass.
- Välj en statisk resursklass när resursen förväntningar varierar under dagen. Till exempel fungerar en statisk resursklass bra när datalagret efterfrågas av många personer. Om skalning datalagret, ändras inte mängden minne som allokerats till användaren. Fler frågor kan därför köras parallellt på systemet.

Att välja rätt minnestilldelningen beror på många faktorer, t.ex. hur mycket data som efterfrågas tabellscheman och olika koppling och välj gruppen predikat. I allmänhet allokera mer minne kan frågor för att slutföra snabbare, men minskar den övergripande samtidigheten. Om samtidighet inte är ett problem, skadar över minnesallokering inte dataflöde. 

Använd olika resursklasser för att justera prestanda. Nästa avsnitt ger en lagrad procedur som hjälper dig att ta reda på den bästa resursklassen.

## <a name="example-code-for-finding-the-best-resource-class"></a>Kodexempel för att hitta den bästa resursklassen
 
Du kan använda följande lagrade procedur på **Gen1 endast** för att ta reda på bevilja samtidighet och minne per resursklass vid en viss SLO och den närmaste bästa resursklassen för CCI aktiviteter på icke-partitionerad CCI tabellen på minne en viss resursklass:

Här är syftet med den här lagrade proceduren:  
1. För att se samtidighet och minne bevilja per resursklass vid en viss Servicenivåmål. Användaren måste ange NULL för både schema- och tabellnamn enligt det här exemplet.  
2. Se den närmaste bästa resursklassen för minnesintensiva CCI åtgärder (belastning, kopiera tabellen återskapa indexet och så vidare) på icke-partitionerad CCI tabellen i en viss resursklass. Den lagrade proceduren använder tabellens schema för att ta reda på den obligatoriska minnestilldelningen.

### <a name="dependencies--restrictions"></a>Beroenden och begränsningar:
- Den här lagrade proceduren är inte avsedd att beräkna minneskravet för en partitionerad cci tabell.    
- Den här lagrade proceduren tar inte minneskravet hänsyn för den VALDA delen av CTAS/INSERT-välja och förutsätter att det är en väljer.
- Den här lagrade proceduren använder en temporär tabell som är tillgänglig i sessionen där den här lagrade proceduren har skapats.    
- Den här lagrade proceduren beror på de aktuella erbjudandena (till exempel maskinvarukonfiguration, DMS-konfiguration) och om någon av som ändras sedan den här lagrade proceduren fungerar inte korrekt.  
- Den här lagrade proceduren beror på befintliga erbjudna samtidighetsgräns och om att ändringar sedan den här lagrade proceduren skulle inte fungerar korrekt.  
- Den här lagrade proceduren är beroende av befintlig klass resurserbjudanden och om att ändringar sedan den här lagrade proceduren skulle inte fungerar korrekt.  

>  [!NOTE]  
>  Om du inte får utdata när du kör lagrad procedur med parametrar som tillhandahålls, kan kommer det finnas två fall. <br />1. Antingen DW-parametern innehåller ett ogiltigt värde för Servicenivåmål <br />2. Eller, det finns inga matchande resursklassen för CCI åtgärden för tabellen. <br />Till exempel på DW100, den högsta minnestilldelningen är 400 MB, och om tabellschemat är tillräckligt bred för att mellan krav på 400 MB.
      
### <a name="usage-example"></a>Användningsexempel:
Syntax:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Antingen ange en NULL-parameter för att extrahera den aktuella DWU från det Datalagret eller tillhandahålla eventuella stöds DWU i form av ”DW100”
2. @SCHEMA_NAME: Ange ett schemanamn i tabellen
3. @TABLE_NAME: Ange ett tabellnamn av intresse

Exempel som kör den här lagrade proceduren:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```
> [!NOTE]
> De värden som definieras i den här versionen av den lagrade proceduren gäller endast för Gen1.
>
>

Följande exempel skapar tabell1 som används i föregående exempel.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Lagrad Procedurdefinition

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar databasanvändare och säkerhet finns i [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Mer information om hur större resursklasser kan förbättra kvaliteten för grupperade columnstore-index, finns i [minne optimeringar för columnstore-komprimering](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
