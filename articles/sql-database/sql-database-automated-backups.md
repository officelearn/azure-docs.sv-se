---
title: "Automatisk, geo-redundant säkerhetskopieringar i Azure SQL Database | Microsoft Docs"
description: "SQL-databasen och skapar en lokal databassäkerhetskopia med några minuters mellanrum automatiskt och använder Azure geo-redundant lagring med läsbehörighet för geo-redundans."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 3ee3d49d-16fa-47cf-a3ab-7b22aa491a8d
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: e779aab97a1b96d4a0e327865e957ecd0d97a278
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="learn-about-automatic-sql-database-backups"></a>Lär dig mer om automatisk säkerhetskopiering i SQL-databas

SQL-databas skapas säkerhetskopiorna av databasen automatiskt och använder Azure-geo-redundant lagring med läsbehörighet (RA-GRS) för att ge geo-redundans. Dessa säkerhetskopior skapas automatiskt och utan extra kostnad. Du behöver inte göra något för att göra dem inträffa. Säkerhetskopiorna av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Du kan konfigurera en princip för långsiktig lagring av säkerhetskopior om du vill behålla säkerhetskopior i din egen lagringsbehållaren. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Vad är en säkerhetskopia av SQL-databasen?

SQL-databasen använder SQL Server-teknik för att skapa [fullständig](https://msdn.microsoft.com/library/ms186289.aspx), [differentiella](https://msdn.microsoft.com/library/ms175526.aspx), och [transaktionsloggen](https://msdn.microsoft.com/library/ms191429.aspx) säkerhetskopior. Säkerhetskopieringarna av transaktionsloggen inträffa normalt var 5-10 minut, ofta baserat på prestanda och mycket Databasaktivitet. Säkerhetskopiering av transaktionsloggar, med fullständig och differentiell säkerhetskopiering kan du återställa en databas till en specifik i tidpunkt till samma server som är värd för databasen. När du återställer en databas räknat tjänsten ut där full, differentiella och transaktionen loggfiler säkerhetskopior som ska återställas.


Du kan använda dessa säkerhetskopieringar till:

* Återställa en databas till en i tidpunkt inom kvarhållningsperioden. Den här åtgärden skapar en ny databas på samma server som den ursprungliga databasen.
* Återställa en borttagen databas till den tid som det har tagits bort eller inom kvarhållningsperioden. Borttagen databas kan bara återställas på samma server som var den ursprungliga databasen har skapats.
* Återställa en databas till en annan geografisk region. På så sätt kan du återställa en geografisk katastrofåterställning när du inte kommer åt din server och databas. En ny databas skapas i en befintlig server som helst i världen. 
* Återställa en databas från en specifik säkerhetskopia som lagras i Azure Recovery Services-valvet. På så sätt kan du återställa en gammal version av databasen att uppfylla en begäran om kompatibilitet eller kör en gammal version av programmet. Se [långsiktig kvarhållning](sql-database-long-term-retention.md).
* Om du vill utföra en återställning, se [återställa databasen från säkerhetskopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure storage termen *replikering* refererar till kopiera filer från en plats till en annan. SQL- *databasreplikering* refererar till att hålla till flera sekundära databaser som synkroniseras med en primär databas. 
> 

## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Hur mycket lagring för säkerhetskopiering ingår kostnadsfritt?
SQL-databasen innehåller upp till 200% av dina högsta etablerade databaslagring säkerhetskopieringslagring utan extra kostnad. Om du har en Standard DB-instans med en etablerade DB storlek på 250 GB, har du till exempel 500 GB lagring för säkerhetskopiering utan extra kostnad. Om databasen överskrider den angivna lagringen för säkerhetskopiering, kan du minska kvarhållningsperioden genom att kontakta Azure-supporten. Ett annat alternativ är att betala för extra säkerhetskopieringslagring som faktureras enligt standardkostnaden geografiskt Redundant lagring med läsbehörighet (RA-GRS). 

## <a name="how-often-do-backups-happen"></a>Hur ofta hända säkerhetskopieringar?
Fullständiga databassäkerhetskopieringar inträffa varje vecka, differentiella säkerhetskopieringar sker vanligtvis var några timmar och transaktionsloggen säkerhetskopieringar göras normalt var 5-10: e minut. Den första kompletta säkerhetskopian kommer omedelbart när en databas har skapats. Det slutförs vanligtvis inom 30 minuter, men det kan ta längre tid när databasen är av betydande storlek. Den första säkerhetskopian kan till exempel ta längre tid på en återställd databas eller en databaskopia. Efter den första kompletta säkerhetskopian alla ytterligare säkerhetskopior schemaläggs automatiskt och hanteras tyst i bakgrunden. Exakt tidtagningen av alla databassäkerhetskopieringar bestäms av SQL Database-tjänsten som det. balanserar den allmänna belastningen på systemet. 

Backup storage geo-replikering inträffar baserat på schemat för Azure Storage-replikering.

## <a name="how-long-do-you-keep-my-backups"></a>Hur länge håller du Mina säkerhetskopior?
Varje säkerhetskopiering i SQL-databas har en kvarhållningsperiod som baseras på den [tjänstnivå](sql-database-service-tiers.md) av databasen. Kvarhållningsperiod för en databas i den:


* Grundläggande tjänstnivå är 7 dagar.
* Standard-tjänstnivå är 35 dagar.
* Premiumnivån är 35 dagar.

Om du nedgradera databasen från tjänstnivåer Standard eller Premium till Basic sparas säkerhetskopieringar i sju dagar. Alla befintliga säkerhetskopior som är äldre än sju dagar är inte längre tillgängliga. 

Om du uppgraderar databasen från grundläggande tjänstnivån till Standard eller Premium behåller SQL Database befintliga säkerhetskopior tills de 35 dagar gammal. Den bevarar nya säkerhetskopior allteftersom de sker för 35 dagar.

Om du tar bort en-databas håller säkerhetskopieringar på samma sätt som för en onlinedatabas SQL-databas. Anta att du tar bort en grundläggande databas som har en kvarhållningsperiod på sju dagar. En säkerhetskopia som är fyra dagar gamla sparas för tre dagar.

> [!IMPORTANT]
> Om du tar bort Azure SQL-server som är värd för SQL-databaser, tas även bort alla databaser som hör till servern och kan inte återställas. Du kan inte återställa en server som har tagits bort.
> 

## <a name="how-to-extend-the-backup-retention-period"></a>Hur du utökar säkerhetskopiering loggperioden?
Om ditt program kräver att säkerhetskopiorna är tillgänglig under längre tid kan du utöka inbyggda kvarhållningsperioden genom att konfigurera den långsiktiga säkerhetskopiering bevarandeprincip för enskilda databaser (LTR princip). På så sätt kan du utöka inbyggda it kvarhållningsperioden från 35 dagar till upp till 10 år. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

När du lägger till LTR principen till en databas med Azure-portalen eller API kopieras veckovisa fullständiga databassäkerhetskopieringar automatiskt till din egen Azure Backup Service-valvet. Om databasen är krypterad med TDE krypteras automatiskt säkerhetskopior i vila.  Säkerhetskopiorna har upphört att gälla, baserat på deras tidsstämpel och LTR principen tas bort automatiskt i Services-valvet.  Så du inte behöver hantera schemat för säkerhetskopiering eller bekymra dig om rensning av de gamla filerna. Restore-API: et stöder säkerhetskopior som lagras i valvet så länge valvet är i samma prenumeration som din SQL-databas. Du kan använda Azure-portalen eller PowerShell för att komma åt dessa säkerhetskopior.

> [!TIP]
> En instruktioner finns i [konfigurera och återställning från Azure SQL Database långsiktig lagring av säkerhetskopior.](sql-database-long-term-backup-retention-configure.md)
>

## <a name="are-backups-encrypted"></a>Krypteras säkerhetskopieringar

När TDE är aktiverat för en Azure SQL database, är säkerhetskopior också krypterade. Alla nya Azure SQL-databaser har konfigurerats med TDE aktiverad som standard. Mer information om TDE finns [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="next-steps"></a>Nästa steg

- Säkerhetskopiorna av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Mer information om andra Azure SQL Database business continuity lösningarna, se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure portal finns [återställa databasen till en tidpunkt med hjälp av Azure portal](sql-database-recovery-using-backups.md).
- Om du vill återställa till en tidpunkt med hjälp av PowerShell Se [återställa databasen till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurera, hantera och återställa från långsiktig lagring av automatiserad säkerhetskopieringar i ett Azure Recovery Services-valv med Azure-portalen, se [hantera långsiktig lagring av säkerhetskopior med Azure-portalen](sql-database-long-term-backup-retention-configure.md).
- Konfigurera, hantera och återställa från långsiktig lagring av automatiserad säkerhetskopieringar i ett Azure Recovery Services-valv med hjälp av PowerShell, se [hantera långsiktig lagring av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).
