---
title: Vanliga frågor och svar om säkerhetskopiering av SQL Server-databaser på Azure Virtual Machines med Azure Backup
description: Hitta svar på vanliga frågor om säkerhetskopiering av SQL Server-databaser på virtuella Azure-datorer med Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: 3b7649a029c6c44cd8a25ea553ff2091f816dd3c
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214834"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Vanliga frågor och svar om SQL Server-databaser som körs på en virtuell Azure-säkerhetskopiering

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar SQL Server-databaser som kör på Azure virtual machines (VMs) och som använder den [Azure Backup](backup-overview.md) service.

> [!NOTE]
> Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion.

## <a name="can-i-throttle-the-backup-speed"></a>Kan jag begränsa hastigheten för säkerhetskopiering?

Ja. Du kan begränsa den hastighet som principen för säkerhetskopiering körs för att minimera effekten på en SQL Server-instans. Så här ändrar du inställningen:
1. På SQL Server-instansen i den *c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin* mapp, skapa den *ExtensionSettingsOverrides.json* fil.
2. I den *ExtensionSettingsOverrides.json* filen, ändra den **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Spara ändringarna och stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?
Nej. Den här funktionen stöds inte.

## <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Kan jag se schemalagda säkerhetskopieringsjobb på menyn jobb?

Nej. Den **säkerhetskopieringsjobb** menyn visar jobbinformation på begäran, men inte schemalagda säkerhetskopieringsjobb. Om alla schemalagda säkerhetskopieringsjobb misslyckas, hittar du information i aviseringar för misslyckade jobbet. Du övervakar alla schemalagda och oplanerade säkerhetskopieringsjobb med [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Framtida databaser automatiskt läggs till för säkerhetskopiering?

Nej. När du ställer in skydd för en SQL Server-instans läggs alla databaser när du väljer alternativet för server. När du ställer in skydd måste du manuellt lägga till nya databaser för att skydda dem. Nya databaser skyddas inte automatiskt.

##  <a name="how-do-i-restart-protection-after-i-change-recovery-type"></a>Hur jag för att starta om skydd när du har ändrat typ av återställning?

Utlös en fullständig säkerhetskopia. Loggsäkerhetskopior börjar som förväntat.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan jag skydda tillgänglighet grupper lokalt?

Nej. Azure Backup skyddar SQL Server-databaser som körs i Azure. Om en tillgänglighetsgrupp (AG) fördelas mellan Azure och lokala datorer, kan AG skyddas endast om den primära repliken körs i Azure. Azure Backup skyddar dessutom endast de noder som körs i samma Azure-region som Recovery Services-valvet.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan jag skydda Tillgänglighetsgrupper i olika regioner?

Azure Backup Recovery Services-valvet kan identifiera och skydda alla noder som finns i samma region som valvet. Om din SQL Server Always On availability group sträcker sig över flera Azure-regioner, Ställ in säkerhetskopian från den region som har den primära noden. Azure Backup kan identifiera och skydda alla databaser i tillgänglighetsgruppen enligt din inställning för säkerhetskopiering. När din inställning för säkerhetskopiering inte uppfylls, säkerhetskopieringar misslyckas och du får felet aviseringen.

## <a name="can-i-exclude-databases-with-autoprotection-enabled"></a>Kan jag utesluta databaser med autoprotection aktiverad?

Nej. Autoprotection [gäller för en hel instans](backup-azure-sql-database.md#enable-auto-protection). Du kan inte använda autoprotection för att selektivt skydda databaser i en instans.

## <a name="can-i-have-different-policies-in-an-autoprotected-instance"></a>Kan jag ha olika principer i en autoprotected-instans?

Om din instans innehåller redan vissa skyddade databaser, fortsätter de skyddas under deras respektive principer även efter att du [aktivera autoprotection](backup-azure-sql-database.md#enable-auto-protection). Men har alla oskyddade databaser och databaser som du lägger till senare bara en enda princip. Du definierar den här principen under **Konfigurera säkerhetskopiering** när du har valt databaserna. I själva verket till skillnad från andra skyddade databaser ändra du inte även principen för en databas som är i en autoprotected-instans.
Det enda sättet att ändra den här databasens principen är att tillfälligt inaktivera autoprotection för instansen. Sedan kan du återaktivera autoprotection för instansen.

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-will-backups-stop"></a>Om jag tar bort en databas från en instans av autoprotected säkerhetskopior stoppas?

Nej. Om en databas har släppts från en instans av autoprotected provas fortfarande säkerhetskopior av databasen. Detta innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande är skyddad.

Det enda sättet att sluta skydda den här databasen är att tillfälligt [inaktivera autoprotection](backup-azure-sql-database.md#enable-auto-protection) på instansen. Sedan, under **Säkerhetskopieringsobjekt** för databasen, väljer **stoppa säkerhetskopiering**. Du kan sedan återaktivera autoprotection för den här instansen.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Varför kan jag inte se en databas som har lagts till för en autoprotected-instans?

En databas som du [lägger till i en instans av autoprotected](backup-azure-sql-database.md#enable-auto-protection) visas inte direkt under skyddade objekt. Det beror på att identifieringen vanligtvis körs var 8:e timme. Men du kan identifiera och skydda nya databaser direkt om du kör en identifiering manuellt genom att välja **återställa databaser**, enligt följande bild.

  ![Identifiera manuellt en nyligen tillagd databas](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopiera SQL Server-databasen](backup-azure-sql-database.md) som körs på en virtuell Azure-dator.
