---
title: Azure SQL Database säkerhetskopieringar för upp till 10 år | Microsoft Docs
description: Lär dig hur Azure SQL Database stöder lagring fullständiga databassäkerhetskopieringar för upp till 10 år.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: b2f3c454ba84c7b892096cc42dcbe2706ab6159f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648300"
---
# <a name="store-azure-sql-database-backups-for-up-to-10-years"></a>Azure SQL Database säkerhetskopieringar för upp till 10 år

Många program har regelverk, kompatibilitet eller andra företag syfte som kräver att du behåller databassäkerhetskopieringar utöver de 7-35 dagar som tillhandahålls av Azure SQL Database [automatiska säkerhetskopieringar](sql-database-automated-backups.md). Med hjälp av funktionen för långsiktig Kvarhållning (LTR), kan du lagra angivna SQL-databasen och fullständiga säkerhetskopieringar i [RA-GRS](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) blob-lagring för upp till 10 år. Du kan sedan återställa någon säkerhetskopia som en ny databas.

> [!IMPORTANT]
> Långsiktig kvarhållning är för närvarande under förhandsgranskning. Befintliga säkerhetskopior som lagras i Azure Recovery Services-tjänsten valvet som en del av föregående förhandsversionen av den här funktionen har migrerats till SQL Azure storage.<!-- and available in the following regions: Australia East, Australia Southeast, Brazil South, Central US, East Asia, East US, East US 2, India Central, India South, Japan East, Japan West, North Central US, North Europe, South Central US, Southeast Asia, West Europe, and West US.-->
>

## <a name="how-sql-database-long-term-retention-works"></a>Så här fungerar långsiktig kvarhållning av SQL-databas

Långsiktig lagring av säkerhetskopior utnyttjar den [automatiska säkerhetskopieringar för SQL-databas](sql-database-automated-backups.md) skapade tar för tidpunkt återställning (PITR). Du kan konfigurera en lång sikt bevarandeprincip för varje SQL-databas och ange hur ofta du behöver kopiera säkerhetskopieringar till långsiktig lagring. Så här aktiverar du den flexibiliteten du kan definiera principen en kombination av fyra parametrar: veckovis säkerhetskopiering Kvarhållning (B), månatliga lagring av säkerhetskopior. (M), årlig säkerhetskopiering Kvarhållning (Y) och veckan på året (WeekOfYear). Om du anger att en säkerhetskopia varje vecka ska kopieras till långsiktig lagring. Om du anger M, kopieras en säkerhetskopiering under den första veckan i månaden för långsiktig lagring. Om du anger Y, kopieras en säkerhetskopia under vecka som anges av WeekOfYear för långsiktig lagring. Varje säkerhetskopiering sparas i långsiktig lagring för den tid som anges av dessa parametrar. 

Exempel:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   3 fullständig säkerhetskopiering varje år sparas i 5 år.

- W = 0, M = 3, Y = 0

   En första fullständig säkerhetskopiering varje månad sparas i tre månader.

- W = 12, M = 0, Y = 0

   Varje fullständig säkerhetskopiering varje vecka sparas för 12 veckor.

- W = 6, M = 12, Y = 10 WeekOfYear = 16

   Varje fullständig säkerhetskopiering varje vecka sparas för 6 veckor. Förutom första fullständig säkerhetskopiering varje månad, som sparas för 12 månader. Förutom den fullständiga säkerhetskopior som gjorts på 16 veckan på året, som sparas för 10 år. 

Följande tabell visar takt och förfallodatum för långsiktiga säkerhetskopior för följande princip:

W = 12 veckor (84 dagar), M = 12 månader (365 dagar), Y = 10 år (3650 dagar), WeekOfYear = 15 (vecka efter 15 April)

   ![LTR exempel](./media/sql-database-long-term-retention/ltr-example.png)


 
Om du skulle ändra ovan principen och uppsättning B = 0 (ingen veckovisa säkerhetskopior) i takt med säkerhetskopior skulle ändra som visas i tabellen ovan efter de markerade datum. Det lagringsutrymme som krävs för att hålla dessa säkerhetskopior skulle minska därefter. 

> [!NOTE]
1. LTR kopior skapas med Azure storage-tjänst så att kopieringen inte påverkar prestanda på den befintliga databasen.
2. Principen gäller för framtida säkerhetskopieringar. T.ex. Om den angivna WeekOfYear har varit när principen har konfigurerats, skapas den första säkerhetskopieringen LTR nästa år. 
3. Om du vill återställa en databas från LTR lagring, kan du välja en specifik säkerhetskopia baserat på dess tidsstämpel.   Databasen kan återställas till en befintlig server under samma prenumeration som den ursprungliga databasen. 
> 

## <a name="configure-long-term-backup-retention"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior

Information om hur du konfigurerar långsiktig kvarhållning med Azure-portalen eller med hjälp av PowerShell finns [konfigurera långsiktig lagring av säkerhetskopior](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Nästa steg

Eftersom databassäkerhetskopieringar skydda data från oavsiktliga skador eller tas bort, är de en viktig del av alla affärskontinuitet och haveriberedskap. Mer information om de andra SQL-databas kontinuitet för företag-lösningarna, se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
