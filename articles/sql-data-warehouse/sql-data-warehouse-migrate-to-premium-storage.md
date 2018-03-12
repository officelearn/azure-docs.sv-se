---
title: Migrera ditt befintliga Azure data warehouse till premium-lagring | Microsoft Docs
description: "Anvisningar för att migrera ett befintligt informationslager till premium-lagring"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 1e216da55a4c425fe112215464cdedb59c8db585
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2018
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrera ditt data warehouse till premium-lagring
Azure SQL Data Warehouse som nyligen lades [premium-lagring för bättre prestanda förutsägbarhet][premium storage for greater performance predictability]. Befintligt datalager för närvarande på standardlagring kan nu migreras till premium-lagring. Du kan dra nytta av automatisk migrering av, eller om du föredrar att reglera när du migrerar (som inbegriper vissa avbrott), kan du göra migreringen själv.

Om du har mer än ett datalager, använder den [schema för automatisk migrering av] [ automatic migration schedule] att avgöra när den flyttas.

## <a name="determine-storage-type"></a>Fastställa lagringstyp
Om du har skapat ett informationslager före följande datum använder du standardlagring.

| **Region** | **Datalagret som skapats före detta datum** |
|:--- |:--- |
| Östra Australien |Den 1 januari 2018 |
| Östra Kina |Den 1 november 2016 |
| Norra Kina |Den 1 november 2016 |
| Centrala Tyskland |Den 1 november 2016 |
| Nordöstra Tyskland |Den 1 november 2016 |
| Västra Indien |Den 1 februari 2018 |
| Västra Japan |Den 1 februari 2018 |
| Norra centrala USA |10 november 2016 |

## <a name="automatic-migration-details"></a>Automatisk migrering av information
Som standard kommer vi att migrera din databas du mellan 6:00 och 06:00:00 i din region lokal tid under den [schema för automatisk migrering av][automatic migration schedule]. Ditt befintliga data warehouse kommer vara obrukbara under migreringen. Migreringen tar ungefär en timme per terabyte lagring per datalagret. Du debiteras inte under delar av automatisk migrering.

> [!NOTE]
> När migreringen är klar att ditt data warehouse tillbaka online och kan användas.
>
>

Microsoft tar följande steg för att slutföra migreringen (dessa inte kräver någon inblandning från din sida). I detta exempel anta att ditt befintliga data warehouse på standardlagring med namnet ”MyDW”.

1. Microsoft byter namn på ”MyDW” till ”MyDW_DO_NOT_USE_ [tidsstämpel]”.
2. Microsoft pausar ”MyDW_DO_NOT_USE_ [tidsstämpel]”. Under den här tiden kan görs en säkerhetskopia. Du kan se flera pausar och återupptar om det uppstår problem under den här processen.
3. Microsoft skapar ett nytt datalager med namnet ”MyDW” på premium-lagring från säkerhetskopior som gjorts i steg 2. ”MyDW” visas inte förrän när återställningen är slutförd.
4. När återställningen är klar ”MyDW” returnerar till samma informationslagerenheter och tillstånd (pausas eller aktiv) som den var före migreringen.
5. När migreringen är klar, Microsoft tar bort ”MyDW_DO_NOT_USE_ [tidsstämpel]”.

> [!NOTE]
> Följande inställningar utför inte som en del av migreringen:
>
> * Granskning på databasnivå måste återaktiveras.
> * Brandväggsregler på databasnivå måste läggas till på nytt. Brandväggsregler på servernivå påverkas inte.
>
>

### <a name="automatic-migration-schedule"></a>Automatisk migrering av schemat
Automatisk migrering sker mellan 6:00 och 6:00:00 (lokal tid per region) under följande avbrott schema.

| **Region** | **Uppskattat startdatum** | **Uppskattat slutdatum** |
|:--- |:--- |:--- |
| Östra Australien |19 mars 2018 |20 mars 2018 |
| Östra Kina |Redan migreras |Redan migreras |
| Norra Kina |Redan migreras |Redan migreras |
| Centrala Tyskland |Redan migreras |Redan migreras |
| Nordöstra Tyskland |Redan migreras |Redan migreras |
| Västra Indien |19 mars 2018 |20 mars 2018 |
| Västra Japan |19 mars 2018 |20 mars 2018 |
| Norra centrala USA |Redan migreras |Redan migreras |

## <a name="self-migration-to-premium-storage"></a>Eget migrering till premium-lagring
Om du vill styra när din driftstopp uppstår, kan du använda följande steg för att migrera ett befintligt datalager på standardlagring till premium-lagring. Om du väljer det här alternativet måste du slutföra eget migreringen innan automatisk migrering börjar i den regionen. Detta säkerställer att du undviker risken för automatisk migrering orsakar en konflikt (avser den [schema för automatisk migrering av][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Egna migreringsanvisningar
Om du vill migrera ditt data warehouse dig själv, säkerhetskopiera och återställa funktioner. Återställ del av migreringen förväntas ta ungefär en timme per terabyte lagring per datalagret. Om du vill behålla samma namn när migreringen är klar, så den [steg för att byta namn på under migreringen][steps to rename during migration].

1. [Pausa] [ Pause] ditt data warehouse. 
2. [Återställa] [ Restore] från din senaste ögonblicksbild.
3. Ta bort ditt befintliga data warehouse på standardlagring. **Om du inte gör det här steget, debiteras du för båda datalager.**

> [!NOTE]
>
> När du återställer ditt informationslager, kontrollera att den senaste återställningspunkten inträffar efter att datalagret har pausats.
>
> Följande inställningar utför inte som en del av migreringen:
>
> * Granskning på databasnivå måste återaktiveras.
> * Brandväggsregler på databasnivå måste läggas till på nytt. Brandväggsregler på servernivå påverkas inte.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Byt namn på datalagret under migreringen (valfritt)
Två databaserna på samma logiska server kan inte ha samma namn. SQL Data Warehouse stöder nu möjligheten att byta namn på ett datalager.

I detta exempel anta att ditt befintliga data warehouse på standardlagring med namnet ”MyDW”.

1. Byt namn på ”MyDW” med hjälp av kommandot ALTER DATABASE. (I det här exemplet vi ska byta namn på den ”MyDW_BeforeMigration”.)  Det här kommandot stoppar alla befintliga transaktioner och måste utföras i master-databasen ska lyckas.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Pausa] [ Pause] ”MyDW_BeforeMigration”. 
3. [Återställa] [ Restore] från din senaste ögonblicksbilden en ny databas med namnet tidigare (till exempel ”MyDW”).
4. Delete "MyDW_BeforeMigration." **Om du inte gör det här steget, debiteras du för båda datalager.**


## <a name="next-steps"></a>Nästa steg
Ändra till premium-lagring ha du också ett ökat antal blob-databasfilerna i den underliggande arkitekturen för ditt informationslager. Återskapa grupperade columnstore-index med hjälp av följande skript om du vill maximera prestandafördelarna med den här ändringen. Skriptet fungerar genom att tvinga vissa av dina befintliga data ytterligare blobbar. Om ingen åtgärd distribuera data naturligt över tid som du kan läsa in mer data i tabeller.

Om det uppstår problem med ditt data warehouse [skapa ett supportärende] [ create a support ticket] och referens ”migreringen till premium-lagring” som möjliga orsaker.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: quickstart-scale-compute-portal.md
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
