<properties
   pageTitle="SQL Data Warehouse förväntningar på förhandsversionen | Microsoft Azure"
   description="Sammanfattning av kapaciteten hos den allmänna förhandsversionen och målen för allmän tillgänglighet för SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# SQL Data Warehouse – förväntningar för förhandsversionen

Den här artikeln beskriver kapaciteten för förhandsversionen av SQL Data Warehouse och våra mål för den allmänt tillgängliga tjänsten (GA). Vi uppdaterar kontinuerligt den här informationen allteftersom kapaciteten för den allmänna förhandsversionen utökas.

Våra mål för SQL Data Warehouse:

- Förutsägbar prestanda och linjär skalbarhet upp till petabyte data
- Hög tillförlitlighet för alla informationslager-uppgifter
- Kort tidsåtgång från datainläsning till datainsikter över relationella och icke-relationella data

Vi arbetar ständigt mot dessa mål under förhandsversionen av SQL Data Warehouse.

## Förutsägbar och skalbar prestanda

Azure SQL Data Warehouse introducerar DWU:er (Data Warehouse Units, informationslagerenheter) som ett sätt att mäta beräkningsresuser (CPU, minne, lagrings-I/O) som finns tillgängligt för informationslagret. En ökning av antalet DWU:er, ökar resurserna. Allteftersom antalet DWU:er ökar, kör SQL Data Warehouse parallella åtgärder (datainläsning eller frågor) över fler distribuerade resurser. Det minskar svarstider och förbättrar prestandan.

Alla informationslager har två grundläggande prestandamått:

- Inläsningshastighet. Antalet poster som kan läsas in i informationslagret per sekund. Vi mäter specifikt antalet poster som kan importeras via PolyBase från Azure Blob Storage till en tabell med lagringsindex med grupperade kolumner.
- Avsökningsintervall. Antalet poster som kan hämtas sekventiellt från informationslagret per sekund. Vi mäter specifikt antalet poster som returneras av en fråga på ett lagringsindex med grupperade kolumner.

Vi mäter vissa viktiga prestandaförbättringar och kommer snart att dela förväntade intervall. Under förhandsversionen genomför vi kontinuerliga förbättringar (som att öka komprimering och cachelagring) för att öka de här intervallen och se till att de skalar på ett förutsägbart sätt.  

## Dataskydd

SQL Data Warehouse lagrar alla data i Azure-lagring med hjälp av lokalt redundant lagring. Flera synkrona kopior av data bevaras i det lokala datacentret för att garantera transparent dataskydd vid lokaliserade fel. 

## Säkerhetskopior

Azure SQL Data Warehouse säkerhetskopierar alla data minst var åttonde timme med hjälp av Azure Storage Snapshots. De här ögonblicksbilderna bevaras i 7 dagar. På så sätt kan data återställas till minst 21 olika tidspunkter unde de senaste 7 dagarna upp till den tidpunkt då den senaste ögonblicksbilden togs. Du kan återställa data från en ögonblicksbild med hjälp av PowerShell eller REST API:er.

## Tillförlitlighet för frågor

SQL Data Warehouse bygger på en arkitektur med massiv parallell bearbetning (MPP). SQL Data Warehouse identifierar och migrerar automatiskt Compute- och Control-nodfel. Dock kan en åtgärd (en datainläsning eller fråga till exempel), misslyckas på grund av ett nodfel eller en migrering. Under förhandsgranskningen gör vi kontinuerliga förbättringar för att kunna slutföra åtgärder trots nodfel.


## Uppgraderingar och avbrott

SQL Data Warehouse kommer att uppgraderas med jämna mellanrum för att lägga till nya funktioner och installera kritiska korrigeringar.  Uppgraderingarna kan vara störande och för tillfället genomförs de inte enligt ett förutsägbart schema.  Om du tycker att det här blir för störande så kan du gärna [skapa ett supportärende][] så att vi kan hjälpa dig att hitta en lösning.


## Nästa steg

[Kom igång][] med den allmänna förhandsvisningen.

<!--Image references-->

<!--Article references-->
[skapa ett supportärende]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Kom igång]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->



<!--HONumber=Jun16_HO2-->


