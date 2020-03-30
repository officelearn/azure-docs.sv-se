---
title: Resursklasser för arbetsbelastningshantering
description: Vägledning för hur du använder resursklasser för att hantera samtidighet och beräkningsresurser för frågor i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 47fd30fbb6e6836d6edf18ac68164d515f3aeb93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350736"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Arbetsbelastningshantering med resursklasser i Azure Synapse Analytics

Vägledning för hur du använder resursklasser för att hantera minne och samtidighet för SQL Analytics-frågor i Azure Synapse.  

## <a name="what-are-resource-classes"></a>Vad är resursklasser

Prestandakapaciteten för en fråga bestäms av användarens resursklass.  Resursklasser är förutbestämda resursgränser i SQL Analytics som styr beräkningsresurser och samtidighet för frågekörning. Resursklasser kan hjälpa dig att konfigurera resurser för dina frågor genom att ange begränsningar för antalet frågor som körs samtidigt och på de beräkningsresurser som tilldelats varje fråga.  Det finns en avvägning mellan minne och samtidighet.

- Mindre resursklasser minskar maximalt minne per fråga, men ökar samtidigheten.
- Större resursklasser ökar det maximala minnet per fråga, men minskar samtidigheten.

Det finns två typer av resursklasser:

- Statiska resursklasser, som är väl lämpade för ökad samtidighet på en datauppsättningsstorlek som är fast.
- Dynamiska resursklasser, som är väl lämpade för datauppsättningar som växer i storlek och behöver ökad prestanda när servicenivån skalas upp.

Resursklasser använder samtidighetsplatser för att mäta resursförbrukning.  [Samtidighetsplatser](#concurrency-slots) förklaras senare i den här artikeln.

- Om du vill visa resursutnyttjandet för resursklasserna finns i Begränsningar för [minne och samtidighet](memory-concurrency-limits.md).
- Om du vill justera resursklassen kan du köra frågan under en annan användare eller [ändra den aktuella användarens resursklassmedlemskap.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Statiska resursklasser

Statiska resursklasser allokerar samma mängd minne oavsett aktuell prestandanivå, som mäts i [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md). Eftersom frågor får samma minnesallokering oavsett prestandanivå, tillåter [skalning av informationslagret](quickstart-scale-compute-portal.md) fler frågor att köra inom en resursklass.  Statiska resursklasser är idealiska om datavolymen är känd och konstant.

De statiska resursklasserna implementeras med dessa fördefinierade databasroller:

- statiskrc10
- statiskrc20
- statiskrc30
- statiskrc40
- statiskrc50
- statiskrc60
- statiskrc70
- statiskrc80

### <a name="dynamic-resource-classes"></a>Dynamiska resursklasser

Dynamiska resursklasser allokerar en varierande mängd minne beroende på den aktuella servicenivån. Statiska resursklasser är bra för högre samtidighet och statiska datavolymer, men dynamiska resursklasser är bättre lämpade för en växande eller varierande mängd data.  När du skalar upp till en större servicenivå får dina frågor automatiskt mer minne.  

De dynamiska resursklasserna implementeras med dessa fördefinierade databasroller:

- smallrc (sn)
- mediumrc (mediumrc)
- largerc (större)
- xlargerc (olikartade)

Minnesallokeringen för varje resursklass är följande. 

| Servicenivå  | smallrc (sn)           | mediumrc (mediumrc)               | largerc (större)                | xlargerc (olikartade)               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c (på andra)         | 25 %               | 25 %                    | 25 %                    | 70 %                    |
| DW200c (på andra)         | 12.5%             | 12.5%                  | 22%                    | 70 %                    |
| DW300c (på andra)         | 8 %                | 10 %                    | 22%                    | 70 %                    |
| DW400c (på andra)         | 6.25%             | 10 %                    | 22%                    | 70 %                    |
| DW500c         | 5 %                | 10 %                    | 22%                    | 70 %                    |
| DW1000c till<br> DW30000c | 3 %       | 10 %                    | 22%                    | 70 %                    |



### <a name="default-resource-class"></a>Standardresursklass

Som standard är varje användare medlem i den dynamiska resursklassen **smallrc**.

Tjänstadministratörens resursklass är fast vid smallrc och kan inte ändras.  Tjänstadministratören är den användare som skapades under etableringsprocessen.  Tjänstadministratören i det här sammanhanget är den inloggning som anges för "Server admin-inloggning" när du skapar en ny SQL Analytics-instans med en ny server.

> [!NOTE]
> Användare eller grupper som definieras som Active Directory-administratör är också tjänstadministratörer.
>
>

## <a name="resource-class-operations"></a>Resursklassåtgärder

Resursklasser är utformade för att förbättra prestanda för datahantering och manipuleringsaktiviteter. Komplexa frågor kan också dra nytta av att köras under en stor resursklass. Frågeprestanda för stora kopplingar och sorter kan till exempel förbättras när resursklassen är tillräckligt stor för att frågan ska kunna köras i minnet.

### <a name="operations-governed-by-resource-classes"></a>Åtgärder som styrs av resursklasser

Dessa åtgärder styrs av resursklasser:

- INFOGA-MARKERA, UPPDATERA, TA BORT
- SELECT (vid fråga användartabeller)
- ALTER INDEX - ÅTERSKAPA ELLER OMORGANISERA
- ÄNDRA OMBYGGNAD AV TABELLEN
- CREATE INDEX
- SKAPA KLUSTRADE COLUMNSTORE-INDEX
- SKAPA TABELL SOM SELECT (CTAS)
- Läsa in data
- Datarörelseåtgärder utförda av Data Movement Service (DMS)

> [!NOTE]  
> SELECT-satser för dynamiska hanteringsvyer (DMV: er) eller andra systemvyer styrs inte av någon av samtidighetsgränserna. Du kan övervaka systemet oavsett hur många frågor som körs på det.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Åtgärder som inte styrs av resursklasser

Vissa frågor körs alltid i smallrc-resursklassen även om användaren är medlem i en större resursklass. Dessa undantagna frågor räknas inte in i samtidighetsgränsen. Om gränsen för samtidighet till exempel är 16 kan många användare välja från systemvyer utan att påverka tillgängliga samtidighetsplatser.

Följande satser är undantagna från resursklasser och körs alltid i smallrc:

- SKAPA ELLER SLÄPP TABELL
- ALTER TABELL ... VÄXLA, DELA ELLER SAMMANFOGA PARTITION
- ÄNDRA INAKTIVERA INDEX
- DROP INDEX
- SKAPA, UPPDATERA ELLER SLÄPPA STATISTIK
- TRUNKERA TABELL
- ÄNDRA TILLSTÅND
- SKAPA INLOGGNING
- SKAPA, ÄNDRA eller SLÄPP ANVÄNDARE
- SKAPA, ÄNDRA ELLER SLÄPP-PROCEDUREN
- SKAPA ELLER SLÄPP-VY
- INFOGA VÄRDEN
- VÄLJ från systemvyer och DMV:er
- Förklara
- Dbcc

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Platser för samtidighet

Samtidighetsplatser är ett praktiskt sätt att spåra de resurser som är tillgängliga för frågekörning. De är som biljetter som du köper för att boka platser på en konsert eftersom sittplatser är begränsad. Det totala antalet samtidighetsplatser per informationslager bestäms av servicenivån. Innan en fråga kan börja köras måste den kunna reservera tillräckligt med samtidighetsplatser. När en fråga är klar släpper den sina samtidighetsplatser.  

- En fråga som körs med 10 samtidighetsplatser kan komma åt 5 gånger fler beräkningsresurser än en fråga som körs med 2 samtidighetsplatser.
- Om varje fråga kräver 10 samtidighetsplatser och det finns 40 samtidighetsplatser, kan endast 4 frågor köras samtidigt.

Endast resursstyrda frågor förbrukar samtidighetsplatser. Systemfrågor och vissa triviala frågor förbrukar inte några platser. Det exakta antalet samtidighetsplatser som förbrukas bestäms av frågans resursklass.

## <a name="view-the-resource-classes"></a>Visa resursklasserna

Resursklasser implementeras som fördefinierade databasroller. Det finns två typer av resursklasser: dynamiska och statiska. Om du vill visa en lista över resursklasserna använder du följande fråga:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Ändra en användares resursklass

Resursklasser implementeras genom att användare tilldelas databasroller. När en användare kör en fråga körs frågan med användarens resursklass. Om en användare till exempel är medlem i rollen staticrc10-databas körs deras frågor med små mängder minne. Om en databasanvändare är medlem i databasrollerna xlargerc eller staticrc80 körs deras frågor med stora mängder minne.

Om du vill öka en användares resursklass använder [du sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) för att lägga till användaren i en databasroll för en stor resursklass.  Nedanstående kod lägger till en användare i rollen largerc databas.  Varje begäran får 22% av systemminnet.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Om du vill minska resursklassen använder [du sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Om "loaduser" inte är medlem eller andra resursklasser går de in i standardresursklassen smallrc med ett minnesbidrag på 3 %.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Resursklassprioritet

Användare kan vara medlemmar i flera resursklasser. När en användare tillhör mer än en resursklass:

- Dynamiska resursklasser har företräde framför statiska resursklasser. Om en användare till exempel är medlem i både mediumrc(dynamic) och staticrc80 (static) körs frågor med mediumrc.
- Större resursklasser har företräde framför mindre resursklasser. Om en användare till exempel är medlem i mediumrc och largerc körs frågor med largerc. På samma sätt, om en användare är medlem i både staticrc20 och statirc80, körs frågor med staticrc80-resursallokeringar.

## <a name="recommendations"></a>Rekommendationer

>[!NOTE]
>Överväg att utnyttja funktioner för hantering av arbetsbelastningar[(arbetsbelastningsisolering,](sql-data-warehouse-workload-isolation.md) [klassificering](sql-data-warehouse-workload-classification.md) och [betydelse)](sql-data-warehouse-workload-importance.md)för mer kontroll över din arbetsbelastning och förutsägbara prestanda.  
>
>

Vi rekommenderar att du skapar en användare som är dedikerad till att köra en viss typ av fråga eller inläsningsåtgärd. Ge användaren en permanent resursklass i stället för att ändra resursklassen ofta. Statiska resursklasser ger större övergripande kontroll över arbetsbelastningen, så vi föreslår att du använder statiska resursklasser innan du överväger dynamiska resursklasser.

### <a name="resource-classes-for-load-users"></a>Resursklasser för inläsningsanvändare

`CREATE TABLE`använder grupperade columnstore-index som standard. Att komprimera data till ett columnstore-index är en minnesintensiv åtgärd och minnestrycket kan minska indexkvaliteten. Minnestryck kan leda till att du behöver en högre resursklass när data läses in. Om du vill vara att säkerställa att belastningen har tillräckligt med minne kan du skapa en användare som är avsedd för att köra laster och tilldela användaren till en högre resursklass.

Det minne som behövs för att bearbeta belastningar effektivt beror på vilken typ av tabell som läses in och datastorleken. Mer information om minneskrav finns i [Maximera radgruppskvaliteten](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

När du har bestämt minneskravet väljer du om du vill tilldela inläsningsanvändaren till en statisk eller dynamisk resursklass.

- Använd en statisk resursklass när tabellminneskraven ligger inom ett visst intervall. Laster körs med lämpligt minne. När du skalar informationslagret behöver inte lasterna mer minne. Med hjälp av en statisk resursklass förblir minnesallokeringarna konstanta. Den här konsekvensen sparar minne och gör att fler frågor kan köras samtidigt. Vi rekommenderar att nya lösningar använder de statiska resursklasserna först eftersom dessa ger större kontroll.
- Använd en dynamisk resursklass när kraven på tabellminne varierar kraftigt. Belastningar kan kräva mer minne än den aktuella DWU- eller cDWU-nivån ger. Skalning av informationslagret ger mer minne att läsa in åtgärder, vilket gör att belastningen kan fungera snabbare.

### <a name="resource-classes-for-queries"></a>Resursklasser för frågor

Vissa frågor är beräkningsintensiva och andra inte.  

- Välj en dynamisk resursklass när frågor är komplexa, men behöver inte hög samtidighet.  Att generera dagliga eller veckovisa rapporter är till exempel ett tillfälligt behov av resurser. Om rapporterna bearbetar stora mängder data ger skalning av informationslagret mer minne till användarens befintliga resursklass.
- Välj en statisk resursklass när resursförväntningarna varierar under dagen. En statisk resursklass fungerar till exempel bra när informationslagret efterfrågas av många. När du skalar informationslagret ändras inte mängden minne som allokerats till användaren. Därför kan fler frågor köras parallellt på systemet.

Korrekt minnesbidrag beror på många faktorer, till exempel mängden data som efterfrågas, tabellschemanas art och olika kopplingar, val och grupppredikater. I allmänhet kan allokering av mer minne frågor slutföras snabbare, men minskar den totala samtidigheten. Om samtidighet inte är ett problem skadar inte överallokering av minne dataflödet.

Om du vill justera prestanda använder du olika resursklasser. I nästa avsnitt visas en lagrad procedur som hjälper dig att ta reda på den bästa resursklassen.

## <a name="example-code-for-finding-the-best-resource-class"></a>Exempelkod för att hitta den bästa resursklassen

Du kan använda följande angivna lagrade procedur för att räkna ut samtidighet och minnesbidrag per resursklass vid en viss SLO och den bästa resursklassen för minnesintensiva CCI-åtgärder på icke-partitionerad CCI-tabell i en viss resursklass:

Här är syftet med denna lagrade procedur:

1. För att se samtidighet och minne bidrag per resurs klass vid en viss SLO. Användaren måste ange NULL för både schema och tabellnamn som visas i det här exemplet.  
2. Om du vill se den bästa resursklassen för de minnesintensiva CCI-åtgärderna (inläsning, kopieringstabell, återskapa index osv.) i icke-partitionerad CCI-tabell i en viss resursklass. Den lagrade proc använder tabellschema för att ta reda på det nödvändiga minnesbidraget.

### <a name="dependencies--restrictions"></a>Beroenden & begränsningar

- Den här lagrade proceduren är inte utformad för att beräkna minneskravet för en partitionerad cci-tabell.
- Den här lagrade proceduren tar inte hänsyn till minneskrav för SELECT-delen av CTAS/INSERT-SELECT och förutsätter att det är en SELECT.
- Den här lagrade proceduren använder en temporär tabell som är tillgänglig i sessionen där den här lagrade proceduren skapades.
- Den här lagrade proceduren beror på aktuella erbjudanden (till exempel maskinvarukonfiguration, DMS-konfiguration) och om något av det ändras fungerar inte den här lagrade processen korrekt.  
- Den här lagrade proceduren beror på befintliga samtidighetsbegränsningserbjudanden och om dessa ändras fungerar inte den här lagrade proceduren korrekt.  
- Den här lagrade proceduren beror på befintliga resursklasserbjudanden och om dessa ändras fungerar inte den här lagrade proceduren korrekt.  

>[!NOTE]  
>Om du inte får utdata efter att ha kört lagrad procedur med parametrar som tillhandahålls, kan det finnas två fall.
>
>1. Antingen DW-parametern innehåller ett ogiltigt SLO-värde
>2. Eller så finns det ingen matchande resursklass för cci-åtgärden i tabellen.
>
>På DW100c är till exempel det högsta tillgängliga minnesbidraget 1 GB och om tabellschemat är tillräckligt brett för att passera kravet på 1 GB.

### <a name="usage-example"></a>Exempel på användning

Syntax:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:Antingen tillhandahålla en NULL-parameter för att extrahera den nuvarande DWU från DW DB eller tillhandahålla någon DWU som stöds i form av "DW100c"
2. @SCHEMA_NAME:Ange ett schemanamn för tabellen
3. @TABLE_NAME:Ange ett tabellnamn för räntan

Exempel som kör den här lagrade processen:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Följande sats skapar Tabell1 som används i föregående exempel.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definition av lagrad procedur

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

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
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
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
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
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar databasanvändare och säkerhet finns [i Skydda en databas i SQL Analytics](sql-data-warehouse-overview-manage-security.md). Mer information om hur större resursklasser kan förbättra indexkvaliteten i klustrade columnstore finns i [Minnesoptimeringar för columnstore-komprimering](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

