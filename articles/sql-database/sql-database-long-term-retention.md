---
title: Långsiktig kvarhållning av säkerhetskopior
description: Lär dig hur Azure SQL Database har stöd för lagring av fullständiga säkerhets kopior av databaser i upp till 10 år via långsiktig bevarande princip.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 05/18/2019
ms.openlocfilehash: d015eea21bcfa499d6751e024a882a7316b7f1a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380760"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database långsiktig kvarhållning

Många program har regler, efterlevnad eller andra affärs behov som kräver att du bevarar databas säkerhets kopiorna utöver de 7-35 dagar som tillhandahålls av Azure SQL Database [Automatisk säkerhets kopiering](sql-database-automated-backups.md). Med hjälp av funktionen för långsiktig kvarhållning (brv) kan du lagra angivna fullständiga SQL Database-säkerhetskopieringar i Azure Blob Storage med Read-Access Geo-redundant lagring i upp till 10 år. Du kan sedan återställa en säkerhets kopia som en ny databas. Mer information om Azure Storage redundans finns [Azure Storage redundans](../storage/common/storage-redundancy.md). 

Lång tids kvarhållning kan aktive ras för enkla databaser och är i begränsad offentlig för hands version för Azure SQL Database hanterade instanser. 

> [!NOTE]
> Du kan använda SQL Agent-jobb för att schemalägga [säkerhets kopiering av skrivskyddade databaser](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) som ett alternativ till vä-hö över 35 dagar.


## <a name="how-sql-database-long-term-retention-works"></a>Så fungerar långsiktig kvarhållning i SQL Database

Långsiktig kvarhållning av säkerhets kopior (brv) utnyttjar de fullständiga säkerhets kopiorna av databasen som [skapas automatiskt](sql-database-automated-backups.md) för att aktivera punkt-tids återställning (PITR). Om en LTR-princip har kon figurer ATS kopieras dessa säkerhets kopior till olika blobbar för långsiktig lagring. Kopieringen är ett bakgrunds jobb som inte påverkar databasens arbets belastning. Principen för vä-hö för varje SQL-databas kan också ange hur ofta säkerhets kopieringarna ska skapas.

Om du vill aktivera vä-hö kan du definiera en princip med en kombination av fyra parametrar: veckovis kvarhållning av säkerhets kopior (W), månatlig kvarhållning av säkerhets kopior (M), årlig kvarhållning av säkerhets kopior (Y) och vecka på år (WeekOfYear). Om du anger a kommer en säkerhets kopia varje vecka att kopieras till långsiktig lagring. Om du anger M kommer den första säkerhets kopian av varje månad att kopieras till långsiktig lagring. Om du anger Y kopieras en säkerhets kopia under den vecka som anges av WeekOfYear till långsiktig lagring. Om den angivna WeekOfYear infaller efter att principen har kon figurer ATS skapas den första säkerhets kopian för LTR under följande år. Varje säkerhets kopia sparas i långtids lagringen enligt de princip parametrar som konfigureras när säkerhets kopian för LTR skapas.

> [!NOTE]
> Alla ändringar i LTR-principen gäller endast för framtida säkerhets kopieringar. Om till exempel veckovis kvarhållning av säkerhets kopior (W), månatlig kvarhållning av säkerhets kopior (M) eller årlig kvarhållning av säkerhets kopior (Y) ändras, kommer den nya inställningen för kvarhållning endast att gälla för nya säkerhets kopior. Kvarhållning av befintliga säkerhets kopior kommer inte att ändras. Om din avsikt är att ta bort gamla LTR-säkerhetskopieringar innan deras kvarhållningsperiod går ut, måste du [ta bort säkerhets kopiorna manuellt](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exempel på LTR-principen:

-  W = 0, M = 0, Y = 5, WeekOfYear = 3

   Den tredje fullständiga säkerhets kopian av varje år sparas i fem år.
   
- W = 0, M = 3, Y = 0

   Den första fullständiga säkerhets kopian av varje månad sparas i tre månader.

- W = 12, M = 0, Y = 0

   Varje veckovis fullständig säkerhets kopiering sparas i 12 veckor.

- W = 6, M = 12, Y = 10, WeekOfYear = 16

   Varje veckovis fullständig säkerhets kopiering sparas i sex veckor. Förutom första fullständiga säkerhets kopiering av varje månad, som sparas i 12 månader. Förutom den fullständiga säkerhets kopieringen som gjorts den sextonde veckan på året, som sparas i 10 år. 

Följande tabell visar takt och förfallo datum för de långsiktiga säkerhets kopiorna för följande princip:

W = 12 veckor (84 dagar), M = 12 månader (365 dagar), Y = 10 år (3650 dagar), WeekOfYear = 15 (vecka efter 15 april)

   ![vä-exempel](./media/sql-database-long-term-retention/ltr-example.png)



Om du ändrar principen ovan och anger W = 0 (inga vecko Visa säkerhets kopior), kommer takt för säkerhets kopior att ändras så som visas i tabellen ovan med de markerade datumen. Det lagrings utrymme som krävs för att behålla säkerhets kopiorna minskar därför. 

> [!IMPORTANT]
> Tiden för de enskilda säkerhets kopieringarna i LTR styrs av Azure SQL Database. Du kan inte manuellt skapa en LTR-säkerhetskopiering eller kontrol lera tiden för att skapa säkerhets kopior. När du har konfigurerat en LTR-princip kan det ta upp till sju dagar innan den första säkerhets kopian i LTR visas i listan över tillgängliga säkerhets kopior.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Kvarhållning av geo-replikering och långsiktig säkerhets kopiering

Om du använder aktiv geo-replikering eller failover-grupper som verksamhets kontinuitets lösning bör du förbereda för eventuell redundans och konfigurera samma LTR-princip för den geo-sekundära databasen. Din LTR Storage-kostnad ökar inte när säkerhets kopiering inte genereras från sekundärerna. Endast när den sekundära blir primär kommer säkerhets kopiorna att skapas. Den säkerställer icke-avbruten generering av säkerhets kopian av LTR När redundansväxlingen utlöses och den primära flyttas till den sekundära regionen. 

> [!NOTE]
> När den ursprungliga primära databasen återställs från ett avbrott som orsakade redundansväxlingen, blir det en ny sekundär. Därför återupptas inte skapandet av säkerhets kopieringen och den befintliga LTR-principen börjar inte gälla förrän den blir primär igen. 

## <a name="managed-instance-support"></a>Stöd för hanterade instanser

Att använda långsiktig kvarhållning av säkerhets kopior med en Azure SQL Database hanterade instanser har följande begränsningar:

- **Begränsad offentlig för hands version** – den här för hands versionen är endast tillgänglig för EA-och CSP-prenumerationer och har begränsad tillgänglighet.  
- [**Endast PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) – det finns för närvarande inget stöd för Azure Portal. VÄ-hö måste vara aktive rad med PowerShell. 

Om du vill begära registrering skapar du ett [support ärende för Azure](https://azure.microsoft.com/support/create-ticket/) under support avsnittet **säkerhets kopiering, återställning och affärs kontinuitet/långsiktig kvarhållning av säkerhets kopior**.


## <a name="configure-long-term-backup-retention"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior

Mer information om hur du konfigurerar långsiktig kvarhållning med hjälp av Azure Portal eller PowerShell finns i [hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Återställ databas från LTR-säkerhetskopiering

Om du vill återställa en databas från den lokala lagrings platsen kan du välja en speciell säkerhets kopia baserat på dess tidsstämpel. Databasen kan återställas till en befintlig server under samma prenumeration som den ursprungliga databasen. Information om hur du återställer databasen från en LTR-säkerhetskopiering med hjälp av Azure Portal eller PowerShell finns i [hantera Azure SQL Database långsiktig kvarhållning av säkerhets kopior](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Nästa steg

Eftersom säkerhets kopiering av databasen skyddar data från oavsiktlig skada eller borttagning, är de en viktig del av all affärs kontinuitet och katastrof återställnings strategi. Mer information om andra SQL Database lösningar för företags kontinuitet finns i [Översikt över affärs kontinuitet](sql-database-business-continuity.md).
