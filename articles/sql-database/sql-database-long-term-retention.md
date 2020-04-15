---
title: Långsiktig kvarhållning av säkerhetskopior
description: Lär dig hur Azure SQL Database stöder lagring av fullständiga säkerhetskopieringar av databaser i upp till 10 år via principen för långsiktig lagring.
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380760"
---
# <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database långtidslodande

Många program har reglerande, efterlevnad eller andra affärsändamål som kräver att du behåller säkerhetskopieringar av databaser efter de 7-35 dagar som tillhandahålls av [automatiska säkerhetskopior i](sql-database-automated-backups.md)Azure SQL Database . Genom att använda funktionen för långsiktig kvarhållning (LTR) kan du lagra angivna fullständiga säkerhetskopior av SQL-databasen i Azure Blob-lagring med geo redundant lagring med läsåtkomst i upp till 10 år. Du kan sedan återställa alla säkerhetskopieringar som en ny databas. Mer information om Azure Storage-redundans finns i [Azure Storage redundans](../storage/common/storage-redundancy.md). 

Lagring av lång tid kan aktiveras för enskilda och poolade databaser och är i begränsad offentlig förhandsversion för hanterade Azure SQL Database-instanser. 

> [!NOTE]
> Du kan använda SQL [Agent-jobb](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) för att schemalägga säkerhetskopior med endast kopior som ett alternativ till LTR längre än 35 dagar.


## <a name="how-sql-database-long-term-retention-works"></a>Så fungerar långsiktig kvarhållning i SQL Database

Långsiktig bevarande av säkerhetskopiering (LTR) utnyttjar de fullständiga säkerhetskopieringar av databasen som [skapas automatiskt](sql-database-automated-backups.md) för att aktivera point-time restore (PITR). Om en LTR-princip har konfigurerats kopieras dessa säkerhetskopior till olika blobbar för långsiktig lagring. Kopian är ett bakgrundsjobb som inte har någon prestandapåverkan på databasarbetsbelastningen. LTR-principen för varje SQL-databas kan också ange hur ofta LTR-säkerhetskopior skapas.

Om du vill aktivera LTR kan du definiera en princip med hjälp av en kombination av fyra parametrar: veckovis kvarhållning av säkerhetskopiering (W), månatlig kvarhållning av säkerhetskopiering (M), årlig kvarhållning av säkerhetskopiering (Y) och vecka på året (WeekOfYear). Om du anger W kopieras en säkerhetskopia varje vecka till långtidslagringen. Om du anger M kopieras den första säkerhetskopian av varje månad till långtidslagringen. Om du anger Y kopieras en säkerhetskopia under veckan som anges av WeekOfYear till långtidslagringen. Om den angivna WeekOfYear är i det förflutna när principen är konfigurerad, skapas den första LTR-säkerhetskopian under det följande året. Varje säkerhetskopia kommer att hållas i långsiktig lagring enligt de principparametrar som konfigureras när LTR-säkerhetskopieringen skapas.

> [!NOTE]
> Alla ändringar av LTR-principen gäller endast för framtida säkerhetskopior. Om till exempel kvarhållning av säkerhetskopiering (W), månatlig kvarhållning av säkerhetskopiering (M) eller årlig kvarhållning av säkerhetskopiering (Y) ändras, gäller den nya bevarandeinställningen endast för nya säkerhetskopior. Bevarandet av befintliga säkerhetskopior kommer inte att ändras. Om din avsikt är att ta bort gamla LTR säkerhetskopior innan deras kvarhållningsperioden löper ut, måste du [manuellt ta bort säkerhetskopior](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure#delete-ltr-backups).
> 

Exempel på LTR-principen:

-  W=0, M=0, Y=5, WeekOfYear=3

   Den tredje fullständiga säkerhetskopian av varje år kommer att sparas i fem år.
   
- W=0, M=3, Y=0

   Den första fullständiga säkerhetskopian av varje månad kommer att hållas i tre månader.

- W=12, M=0, Y=0

   Varje vecka full backup kommer att hållas i 12 veckor.

- W=6, M=12, Y=10, WeekOfYear=16

   Varje vecka full backup kommer att hållas i sex veckor. Utom första fullständiga backup av varje månad, som kommer att hållas i 12 månader. Förutom hela backup tas på 16: e veckan på året, som kommer att hållas i 10 år. 

Följande tabell illustrerar kadensen och utgången av de långsiktiga säkerhetskopieringarna för följande princip:

W=12 veckor (84 dagar), M=12 månader (365 dagar), Y=10 år (3650 dagar), WeekOfYear=15 (vecka efter den 15 april)

   ![ltr exempel](./media/sql-database-long-term-retention/ltr-example.png)



Om du ändrar ovanstående princip och anger W=0 (inga veckovisa säkerhetskopior) ändras kopieringskadens kadens som visas i tabellen ovan med de markerade datumen. Lagringsbeloppet som behövs för att hålla dessa säkerhetskopior skulle minska i enlighet med detta. 

> [!IMPORTANT]
> Tidpunkten för de enskilda LTR-säkerhetskopiorna styrs av Azure SQL Database. Du kan inte manuellt skapa en LTR-säkerhetskopia eller styra tidpunkten för säkerhetskopieringen. När du har konfigurerat en LTR-princip kan det ta upp till 7 dagar innan den första LTR-säkerhetskopieringen visas i listan över tillgängliga säkerhetskopior.  
> 

## <a name="geo-replication-and-long-term-backup-retention"></a>Geo-replikering och långsiktig lagring av säkerhetskopiering

Om du använder aktiva geo-replikerings- eller redundansgrupper som lösning för affärskontinuitet bör du förbereda dig för eventuella redundans och konfigurera samma LTR-princip i den geo-sekundära databasen. Din LTR-lagringskostnad ökar inte eftersom säkerhetskopior inte genereras från sekundärerna. Endast när den sekundära blir primär kommer säkerhetskopiorna att skapas. Det säkerställer icke-avbruten generering av LTR-säkerhetskopior när redundansen utlöses och den primära flyttar till den sekundära regionen. 

> [!NOTE]
> När den ursprungliga primära databasen återställs från ett avbrott som orsakade redundansen blir den en ny sekundär. Därför återupptas inte skapandet av säkerhetskopiering och den befintliga LTR-principen börjar inte gälla förrän den blir den primära igen. 

## <a name="managed-instance-support"></a>Stöd för hanterad instans

Att använda långsiktig lagring av säkerhetskopiering med en hanterad Azure SQL Database-instans har följande begränsningar:

- **Begränsad offentlig förhandsversion** – Den här förhandsversionen är endast tillgänglig för EA- och CSP-prenumerationer och är beroende av begränsad tillgänglighet.  
- [**Endast PowerShell**](sql-database-managed-instance-long-term-backup-retention-configure.md) - Det finns för närvarande ingen Azure-portalsupport. LTR måste vara aktiverat med PowerShell. 

Om du vill begära registrering skapar du en [Azure-supportbiljett](https://azure.microsoft.com/support/create-ticket/) under supportavsnittet **Säkerhetskopiering, återställning och affärskontinuitet/ Långsiktig lagring av säkerhetskopiering**.


## <a name="configure-long-term-backup-retention"></a>Konfigurera långsiktig kvarhållning av säkerhetskopior

Mer information om hur du konfigurerar långsiktig kvarhållning med Azure-portalen eller PowerShell finns i [Hantera Azure SQL Database långtidslagring av säkerhetskopiering](sql-database-long-term-backup-retention-configure.md).

## <a name="restore-database-from-ltr-backup"></a>Återställa databasen från LTR-säkerhetskopiering

Om du vill återställa en databas från LTR-lagringen kan du välja en specifik säkerhetskopia baserat på dess tidsstämpel. Databasen kan återställas till valfri befintlig server under samma prenumeration som den ursprungliga databasen. Mer information om hur du återställer databasen från en LTR-säkerhetskopia med Azure-portalen eller PowerShell finns i [Hantera Azure SQL Database för långsiktig lagring av säkerhetskopiering](sql-database-long-term-backup-retention-configure.md).

## <a name="next-steps"></a>Nästa steg

Eftersom säkerhetskopiering av databaser skyddar data från oavsiktlig skada eller borttagning är de en viktig del av alla affärskontinuiteter och katastrofåterställningsstrategier. Mer information om de andra lösningarna för affärskontinuitet i SQL Database finns i [Översikt över kontinuitet i affärskontinuitet](sql-database-business-continuity.md).
