---
title: Hantering av samtidighet och arbetsbelastning i SQL Data Warehouse | Microsoft Docs
description: "Förstå samtidighet och arbetsbelastningen hantering i Azure SQL Data Warehouse för utveckling av lösningar."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Hantering av samtidighet och arbetsbelastning i SQL Data Warehouse
För att leverera förutsägbar prestanda i skala, Microsoft Azure SQL Data Warehouse hjälper dig styra samtidighet nivåer och resursfördelningar som minne och CPU-prioritering. Den här artikeln ger en introduktion till begreppet samtidighet och arbetsbelastningen hantering, förklarar hur båda funktionerna har genomförts och hur du kan kontrollera dem i ditt data warehouse. Hantering av SQL Data Warehouse arbetsbelastning är avsedda att hjälpa dig stöd för miljöer med flera användare. Det är inte avsedd för flera innehavare arbetsbelastningar.

## <a name="concurrency-limits"></a>Concurrency-gränser
SQL Data Warehouse kan upp till 1 024 samtidiga anslutningar. Alla 1 024 anslutningar kan skicka frågor samtidigt. För att optimera genomflödet dock kö SQL Data Warehouse några frågor för att säkerställa att varje fråga får en minimal minnestilldelningen. Queuing inträffar vid körning i frågan. Genom queuing frågor när samtidighet gränser nås, öka SQL Data Warehouse totala genomflödet genom att säkerställa att aktiva frågor får åtkomst till resurser ytterst nödvändigt minne.  

Concurrency gränser styrs av två begrepp: *samtidiga frågor* och *samtidighet fack*. Det måste köras i både frågan samtidighet gränsen och samtidighet fack tilldelning att köra en fråga.

* Samtidiga frågor är frågor som körs på samma gång. SQL Data Warehouse stöder upp till 32 samtidiga frågor på större DWU-storlekar.
* Concurrency platser tilldelas baserat på DWU. Varje 100 DWU innehåller 4 samtidighet platser. Till exempel en DW100 allokerar 4 samtidighet platser och DW1000 allokerar 40. Varje fråga förbrukar en eller flera samtidiga platser, beroende på [resursklassen](#resource-classes) av frågan. Frågor som körs i resursklassen smallrc använda en concurrency-plats. Frågor som körs i en högre resursklassen förbruka mer samtidighet platser.

I följande tabell beskrivs gränsvärdena för både samtidiga frågor och samtidighet fack vid olika DWU-storlekar.

### <a name="concurrency-limits"></a>Concurrency-gränser
| DWU | Maximalt antal samtidiga frågor | Concurrency fack allokerade |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

När något av dessa tröskelvärden uppfylls är nya frågor i kö och utförs på grundval först in, först ut.  En frågor har slutförts, och antalet frågor och fack understiger gränserna som släpps köade frågor. 

> [!NOTE]  
> *Välj* frågor körs enbart på hantering av dynamiska vyer (av DMV: er) eller katalogvyer inte regleras av någon av gränserna som samtidighet. Du kan övervaka systemets oavsett antalet frågor som körs på den.
> 
> 

## <a name="resource-classes"></a>Resursklasser
Resursklasser hjälper dig att kontrollera minnesallokeringen och processorcyklerna som en fråga tilldelas. Du kan tilldela en användare i form av databasroller två typer av resursklasser. De två typerna av resursklasser är följande:
1. Dynamiska resurs-klasser (**smallrc mediumrc, largerc xlargerc**) allokera en variabel mängd minne beroende på den aktuella DWU. Det innebär att när du skalar upp till en större DWU dina frågor automatiskt få mer minne. 
2. Statiska resurs-klasser (**staticrc10 staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) allokera samma mängd minne oavsett aktuella DWU (förutsatt att själva DWU har tillräckligt med minne). Det innebär att större dwu: er, du kan köra flera frågor i varje resursklassen samtidigt.

Användare i **smallrc** och **staticrc10** ges en mindre mängd minne och kan dra nytta av högre samtidighet. Däremot användare tilldelade **xlargerc** eller **staticrc80** ges stora mängder minne, och därför färre deras frågor kan köras samtidigt.

Som standard varje användare är medlem i små resursklassen **smallrc**. Proceduren `sp_addrolemember` används för att öka resursklassen och `sp_droprolemember` används för att minska resursklassen. Det här kommandot skulle till exempel öka resursklassen för loaduser till **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Frågor som inte följer resursklasser

Det finns några typer av frågor som inte omfattas av en större minnesallokering. Systemet ignorerar sina allokering av klassen och alltid köra dessa frågor i små resursklassen i stället. Om de här frågorna körs alltid i små resursklassen, kan de köras när samtidighet platser är under hög belastning och de kommer inte använda fler platser än vad som behövs. Se [resurs klassen undantag](#query-exceptions-to-concurrency-limits) för mer information.

## <a name="details-on-resource-class-assignment"></a>Information om resurs klassen tilldelning


Några få mer information om resursklass:

* *ALTER role* behörighet krävs för att ändra resursklassen för en användare.
* Du kan lägga till en användare till en eller flera av de högre resurs klasserna, åsidosätter dynamisk resursklasser statiska resursklasser. Det vill säga om en användare har tilldelats både **mediumrc**(dynamisk) och **staticrc80**(statisk) **mediumrc** är resursklass som har lösts in.
 * När en användare har tilldelats fler än en resursklass i en viss klass resurstyp (mer än en dynamisk resursklassen eller mer än en statisk resursklassen), funktion den högsta resursklassen. Om en användare har tilldelats både mediumrc och largerc funktion som är högre resursklass (largerc). Och om en användare har tilldelats både **staticrc20** och **statirc80**, **staticrc80** är hanteras.
* Resursklass av den administrativa systemanvändaren kan inte ändras.

Ett detaljerat exempel finns [ändrar användaren resurs klassexempel](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Minnesallokering
Det finns för- och nackdelar för ett ökande antal resursklassen för en användare. Öka en resursklass för en användare får sina frågor tillgång till mer minne, vilket kan innebära att köra frågor snabbare.  Högre resurs klasserna dock minska också antalet frågor som kan köras. Det här är en kompromiss mellan tilldelning av stora mängder minne till en enda fråga eller att tillåta att andra frågor som måste också minnesallokering körs samtidigt. Om en användare får hög tilldelning av minne för en fråga, andra användare inte tillgång till att samma minne för att köra en fråga.

I följande tabell mappar det minne som allokerats för varje distribution av DWU och resurs-klassen.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Minnesallokering per distribution för dynamisk resursklasser (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1,600 |
| DW500 |100 |400 |800 |1,600 |
| DW600 |100 |400 |800 |1,600 |
| DW1000 |100 |800 |1,600 |3,200 |
| DW1200 |100 |800 |1,600 |3,200 |
| DW1500 |100 |800 |1,600 |3,200 |
| DW2000 |100 |1,600 |3,200 |6,400 |
| DW3000 |100 |1,600 |3,200 |6,400 |
| DW6000 |100 |3,200 |6,400 |12,800 |

I följande tabell mappar det minne som allokerats till varje distribution av DWU och statiska resursklassen. Observera att klasserna högre resurs har sina minne minskas för att respektera övergripande DWU-gränser.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Minnesallokering per distribution för statiska resursklasser (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW500 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW600 |100 |200 |400 |800 |1,600 |1,600 |1,600 |1,600 |
| DW1000 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1200 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW1500 |100 |200 |400 |800 |1,600 |3,200 |3,200 |3,200 |
| DW2000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW3000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |6,400 |
| DW6000 |100 |200 |400 |800 |1,600 |3,200 |6,400 |12,800 |

Från föregående tabell kan du se att en fråga som körs på en DW2000 i den **xlargerc** resursklassen skulle ha åtkomst till 6 400 MB minne inom 60 distribuerade databaser.  Det finns 60 distributioner i SQL Data Warehouse. För att beräkna den totala minnesallokering för en fråga i en viss resurs bör därför ovanstående värden multipliceras med 60.

### <a name="memory-allocations-system-wide-gb"></a>Minne-allokeringar systemomfattande (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Från den här tabellen systemomfattande minnesallokering, ser du en fråga som körs på en DW2000 i klassen xlargerc resurs tilldelas 375 GB minne totalt (6 400 MB * 60 distributioner / 1 024 att konvertera till GB) över hela ditt SQL Data Warehouse.

Samma beräkning gäller statiska resursklasser.
 
## <a name="concurrency-slot-consumption"></a>Concurrency fack förbrukning  
SQL Data Warehouse ger mer minne till frågor som körs i högre resursklasser. Minnet är en fast resurs.  Därför mer minne som allokerats per fråga, färre samtidiga frågor kan köra. I följande tabell reiterates alla tidigare begreppen i en enda vy som visar antalet samtidiga fack som är tillgängliga genom DWU och de platser som används av varje resursklassen.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Fördelningen och förbrukningen av samtidighet fack för dynamisk resursklasser  
| DWU | Maximalt antal samtidiga frågor | Concurrency fack allokerade | Platser som används av smallrc | Platser som används av mediumrc | Platser som används av largerc | Platser som används av xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Fördelningen och förbrukningen av samtidighet fack för statiska resursklasser  
| DWU | Maximalt antal samtidiga frågor | Concurrency fack allokerade |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Du kan se som kör SQL Data Warehouse som DW1000 allokerar högst 32 samtidiga frågor och totalt 40 samtidighet fack från dessa tabeller. Om alla användare kör i smallrc, skulle 32 samtidiga förfrågningar tillåts eftersom varje fråga förbrukar 1 samtidighet plats. Om alla användare på en DW1000 kördes i mediumrc, varje fråga skulle allokeras 800 MB per distribution för en totala minnesallokering 47 GB per fråga och samtidighet skulle vara begränsad till 5 användare (40 samtidighet fack / 8 kortplatser per mediumrc användare).

## <a name="selecting-proper-resource-class"></a>Att välja rätt resursklassen  
Det är en bra idé att permanent tilldela användare till en resursklass i stället för att ändra deras resursklasser. Till exempel skapa belastningar grupperade columnstore-tabeller högre kvalitet index när mer minne allokeras. Skapa en användare för att överföra data och permanent tilldela användaren till en högre resursklassen för att säkerställa att belastningen har åtkomst till högre minne.
Det finns ett antal rekommendationer för här. Som nämnts ovan är SQL DW stöder två typer av klassen resurstyper: statisk resursklasser och dynamisk resursklasser.
### <a name="loading-best-practices"></a>Inläsning av bästa praxis
1.  Om förväntningarna belastningar med vanlig mängd data, är en statisk resursklassen ett bra alternativ. Senare, när du ökar få mer dataresurser, kommer datalagret att kunna köra flera samtidiga frågor out-of-the-box, eftersom belastningen användaren inte förbruka mer minne.
2.  Om förväntningarna större belastningar i vissa fall, är en dynamisk resursklassen bra. Senare, när du ökar få mer dataresurser, får Läs in användaren mer minne out-of-the-box, därför att tillåta att belastningen utföra snabbare.

Det minne som behövs för att bearbeta belastningar effektivt beror på tabellen som lästs in och hur mycket data som bearbetas. Till exempel kräver läsa in data i CCI tabeller minne så att CCI rowgroups nå uppfylldes. Mer information finns i Columnstore-index - vägledning för datainläsning.

Som bästa praxis rekommenderar vi att du använder minst 200MB minne för belastning.

### <a name="querying-best-practices"></a>Frågar bästa praxis
Frågor har olika krav beroende på deras komplexitet. Öka minnesmängd per fråga eller öka parallellkörning är båda giltiga sätt att utöka totala genomflödet beroende fråga behov.
1.  Om förväntningarna regelbundna, komplexa frågor (till exempel att generera rapporter för dagliga och veckovisa) och behöver inte dra nytta av samtidighet, dynamiska resursklassen är ett bra alternativ. Om systemet har mer data att bearbeta, ger skala upp datalagret därför automatiskt mer minne till användaren som kör frågan.
2.  Om förväntningarna variabel eller profil över samtidighet mönster (till exempel om databasen efterfrågas via ett webbgränssnitt brett tillgänglig), statiska resursklassen är ett bra alternativ. Senare, när skala upp till data warehouse kommer användaren som är associerad med den statiska resursklassen automatiskt att kunna köra flera samtidiga frågor.

Genom att välja rätt minnestilldelningen beroende på behovet av din fråga är icke-trivial som beror på många faktorer, till exempel hur mycket data som efterfrågas, vilka slags tabellscheman, och olika koppling, val och grupp-predikat. Från en allmän synvinkel allokera mer minne tillåter frågor för att slutföra snabbare, men skulle minskar den övergripande. Om samtidighet inte är ett problem, skadas över minnesallokering inte. För att finjustera genomströmning kan försök olika varianter av resursklasser krävas.

Du kan använda följande lagrade procedur för att ta reda på samtidighet och minne bevilja per resursklassen vid en given SLO och närmaste bästa resursklassen för minne beräkningsintensiva CCI åtgärder på CCI partitionerade tabellen vid en viss resurs-klass:

#### <a name="description"></a>Beskrivning:  
Här är syftet med den här lagrade proceduren:  
1. För att hjälpa användare att ta reda på bevilja samtidighet och minne per resursklassen vid ett givet Servicenivåmål. Användaren måste ange NULL för både schema- och tablename för den här som visas i exemplet nedan.  
2. För att användaren ta reda på den närmaste bästa resursklassen för minne intensed CCI åtgärder (belastning, kopiera tabellen återskapa index, etc.) på icke partitionerad CCI tabell till en viss resurs-klass. Den lagrade proceduren använder tabellschemat för att ta reda på minnestilldelningen krävs för den här.

#### <a name="dependencies--restrictions"></a>Beroenden och begränsningar:
- Den här lagrade proceduren är inte avsedd att beräkna minnesutrymmet för partitionerad cci tabellen.    
- Den här lagrade proceduren tar inte minneskravet hänsyn för den VALDA delen av CTAS/INSERT-Välj och förutsätter att det ska vara ett enkelt SELECT.
- Den här lagrade proceduren använder en temporär tabell så det kan användas i sessionen där den här lagrade proceduren har skapats.    
- Den här lagrade proceduren beror på de aktuella erbjudandena (t.ex. maskinvarukonfiguration, DMS-konfiguration) och om någon av som ändras sedan denna lagrade procedur fungerar inte korrekt.  
- Den här lagrade proceduren beror på befintliga erbjudna samtidighet gränsen och om att ändringar sedan den här lagrade proceduren fungerar inte korrekt.  
- Denna lagrade procedur beror på befintlig resurs klassen erbjudanden och om att ändringar sedan detta lagrade procedur wuold fungerar inte korrekt.  

>  [!NOTE]  
>  Om du inte får utdata när du kör lagrad procedur med parametrar som ges det kan vara två fall. <br />1. Antingen DW-parametern innehåller ett ogiltigt värde för Servicenivåmål <br />2. ELLER så finns det ingen matchande resursklassen för CCI åtgärd om tabellnamn angavs. <br />Exempelvis är DW100, högsta minnestilldelningen 400MB och om tabellschemat litet mellan kravet 400 MB.
      
#### <a name="usage-example"></a>Exempel på användning:
Syntax:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU:Antingen ange en NULL-parameter om du vill extrahera den aktuella DWU från databasen för Datalagret eller ange eventuella stöds DWU i form av 'DW100'
2. @SCHEMA_NAME:Ange ett namn på tabellen
3. @TABLE_NAME:Ange ett tabellnamn av intresse

Exempel köra den här lagrade proceduren:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Tabell 1 används i ovanstående exempel kunde skapas enligt nedan:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Här är definitionen för lagrade proceduren:
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
(   @DWU VARCHAR(7),
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

-- Creating ref. temptable (CTAS) to hold mapping info.
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

## <a name="query-importance"></a>Vikten av frågan
SQL Data Warehouse implementerar resursklasser med hjälp av arbetsbelastningsgrupper. Det finns totalt åtta arbetsbelastningsgrupper som styr beteendet för resursklasser över olika DWU-storleken. För alla DWU använder SQL Data Warehouse bara fyra grupperna åtta arbetsbelastning. Detta är meningsfullt eftersom varje arbetsbelastningsgruppen har tilldelats någon av fyra resursklasser: smallrc mediumrc, largerc, eller xlargerc. Vikten av att förstå grupperna arbetsbelastning är att vissa av dessa arbetsbelastningsgrupper som har angetts till högre *vikten*. Vikten används för CPU schemaläggning. Frågor som körs med hög prioritet får tre gånger så mycket CPU-cykler än de med medelhög prioritet. Därför avgör samtidighet fack mappningar också CPU-prioritet. När en fråga förbrukar 16 eller flera platser, körs det som hög prioritet.

Följande tabell visar vikten mappningar för varje arbetsbelastning.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Arbetsbelastningen gruppmappningar till samtidighet platser och betydelse
| Arbetsbelastningsgrupper | Concurrency fack mappning | MB / Distribution | Vikten mappning |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Medel |
| SloDWGroupC01 |2 |200 |Medel |
| SloDWGroupC02 |4 |400 |Medel |
| SloDWGroupC03 |8 |800 |Medel |
| SloDWGroupC04 |16 |1,600 |Hög |
| SloDWGroupC05 |32 |3,200 |Hög |
| SloDWGroupC06 |64 |6,400 |Hög |
| SloDWGroupC07 |128 |12,800 |Hög |

Från den **fördelningen och förbrukningen av samtidighet fack** diagram, ser du att en DW500 1, 4, 8 och 16 samtidighet fack för smallrc, mediumrc, largerc och xlargerc, respektive. Du kan slå dessa värden upp i det föregående att hitta vikten för varje resurs.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>DW500 mappning av resursklasser betydelse
| Resursklass | Arbetsbelastningsgruppen | Concurrency-platser som används | MB / Distribution | Betydelse |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Medel |
| mediumrc |SloDWGroupC02 |4 |400 |Medel |
| largerc |SloDWGroupC03 |8 |800 |Medel |
| xlargerc |SloDWGroupC04 |16 |1,600 |Hög |
| staticrc10 |SloDWGroupC00 |1 |100 |Medel |
| staticrc20 |SloDWGroupC01 |2 |200 |Medel |
| staticrc30 |SloDWGroupC02 |4 |400 |Medel |
| staticrc40 |SloDWGroupC03 |8 |800 |Medel |
| staticrc50 |SloDWGroupC03 |16 |1,600 |Hög |
| staticrc60 |SloDWGroupC03 |16 |1,600 |Hög |
| staticrc70 |SloDWGroupC03 |16 |1,600 |Hög |
| staticrc80 |SloDWGroupC03 |16 |1,600 |Hög |

Du kan använda följande DMV-frågan granskar skillnaderna i minnet resursallokeringen i detalj ur resursstyrningen eller för att analysera active och historisk användning av arbetsbelastningsgrupper när du felsöker.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Frågor som följer gränser för samtidig användning
De flesta frågor regleras av resursklasser. De här frågorna måste få plats inuti båda samtidiga frågan och samtidighet fack tröskelvärdena. En användare kan inte välja att undanta en fråga från concurrency fack-modell.

Om du vill upprepar, respektera följande påståenden resursklasser:

* INFOGA VÄLJER
* UPPDATERING
* TA BORT
* Välj (när du frågar användartabeller)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* SKAPA INDEX
* SKAPA DET GRUPPERADE COLUMNSTORE-INDEX
* SKAPA TABLE AS SELECT (CTAS)
* Läsa in data
* Dataflyttsåtgärderna utförs av Data Movement Service (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Frågan undantag samtidighet gränserna
Några frågor följdes inte resursklassen som användaren har tilldelats. Sådana undantag samtidighet gränserna görs när minnesresurserna som behövs för ett visst kommando är låg, ofta eftersom kommandot är en metadata-åtgärd. Syftet med sådana undantag är att undvika större minnesallokering för frågor som behöver dem aldrig. I dessa fall används standard små resursklassen (smallrc) alltid oavsett faktiska resursklassen för användaren. Till exempel `CREATE LOGIN` körs alltid i smallrc. De resurser som krävs för att utföra den här åtgärden är mycket låg, så det inte vara klokt att inkludera frågan i samtidighet fack modellen.  De här frågorna också begränsas inte av antalet samtidiga 32 användare, ett obegränsat antal dessa frågor kan köra till högst 1 024 sessioner session.

Följande uttryck följdes inte resursklasser:

* Skapa eller ta bort tabell
* ALTER TABLE... VÄXELN, dela och slå samman partitionen
* ALTER INDEX INAKTIVERA
* DROP INDEX
* Skapa, uppdatera och ta bort statistik
* TRUNKERA TABELLEN
* ÄNDRA TILLSTÅND
* SKAPA INLOGGNING
* Skapa, ändra eller släppa användaren
* Skapa, ändra eller släppa proceduren
* Skapa eller ta bort vy
* LÄGGA TILL VÄRDEN
* Välj systemvyer och av DMV: er
* FÖRKLARAR
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a>Ändra ett exempel på användaren resurs klass
1. **Skapa inloggning:** öppna en anslutning till din **master** databasen på SQLServer som värd för SQL Data Warehouse-databas och kör följande kommandon.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > Det är en bra idé att skapa en användare i master-databasen för Azure SQL Data Warehouse-användare. Skapa en användare i master tillåter en användare att logga in med verktyg som SSMS utan att ange ett databasnamn.  Det gör också att de använder object explorer för att visa alla databaser på en SQLServer.  Mer information om att skapa och hantera användare finns [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Skapa SQL Data Warehouse användare:** öppna en anslutning till den **SQL Data Warehouse** databasen och kör följande kommando.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Bevilja behörighet:** i följande exempel beviljas `CONTROL` på den **SQL Data Warehouse** databas. `CONTROL`databasen är motsvarigheten till db_owner i SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Öka resursklass:** använder du följande fråga för att lägga till en användare till en högre roll för arbetsbelastning.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Minska resursklass:** använder du följande fråga för att ta bort en användare från en roll för arbetsbelastning.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Det går inte att ta bort en användare från smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Köade frågan identifiering och andra av DMV: er
Du kan använda den `sys.dm_pdw_exec_requests` DMV att identifiera frågor som väntar i en kö samtidighet. Frågar väntar på en plats för samtidighet har statusen **avbruten**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

Roller för hantering av arbetsbelastning kan visas med `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

Följande fråga visar vilken roll som varje användare har tilldelats.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

SQL Data Warehouse har följande vänta typer:

* **LocalQueriesConcurrencyResourceType**: frågor som är placerade utanför samtidighet fack ramen. DMV frågor och system fungerar som `SELECT @@VERSION` är exempel på lokala frågor.
* **UserConcurrencyResourceType**: frågor som visas inuti samtidighet fack ramen. Frågor mot tabeller slutanvändarens representerar exempel som använder den här resurstypen.
* **DmsConcurrencyResourceType**: Väntar till följd av dataflyttsåtgärderna.
* **BackupConcurrencyResourceType**: den här vänta anger att en databas säkerhetskopieras. Det maximala värdet för den här resurstypen är 1. Om flera säkerhetskopieringar har begärts samtidigt, kommer de andra kö.

Den `sys.dm_pdw_waits` DMV kan användas för att se vilka resurser som väntar på en begäran.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

Den `sys.dm_pdw_resource_waits` DMV visar endast de resursen väntar som används av en given fråga. Väntetiden för resursen endast mäter den tid som väntar på resurser som ska tillhandahållas, till skillnad från signal väntetiden, vilket är den tid det tar för de underliggande SQL-servrarna att schemalägga frågan till Processorn.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

Den `sys.dm_pdw_wait_stats` DMV kan användas för analys av historiska trender för väntar.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Nästa steg
Mer information om hur du hanterar databasanvändare och säkerhet finns [skydda en databas i SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Mer information om hur större resursklasser kan förbättra kvaliteten för grupperade columnstore-index, finns [bygga om index för att förbättra kvaliteten segment].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[bygga om index för att förbättra kvaliteten segment]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
