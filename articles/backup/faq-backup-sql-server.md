---
title: Vanliga frågor och svar om säkerhetskopiering av SQL Server-databaser på Azure Virtual Machines med Azure Backup
description: Innehåller svar på vanliga frågor om säkerhetskopiering av SQL Server-databaser på virtuella Azure-datorer med Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 8/16/2018
ms.author: sogup
ms.openlocfilehash: a14406733ff60d53d4bf7792ff0c9a015c57d9b3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430782"
---
# <a name="faq-on-sql-server-running-on-azure-vm-backup"></a>Vanliga frågor och svar på SQL Server på Azure-säkerhetskopiering

Den här artikeln innehåller vanliga frågor och svar om säkerhetskopiering av SQL Server-databaser som körs på virtuella Azure-datorer med den [Azure Backup](backup-overview.md) service.

> [!NOTE]
> Den här funktionen är för närvarande i offentlig förhandsversion.



## <a name="can-i-throttle-the-backup-speed"></a>Kan jag begränsa hastigheten för säkerhetskopiering?

Ja. Du kan begränsa den hastighet som principen för säkerhetskopiering körs för att minimera effekten på en SQL Server-instans. Så här ändrar du inställningen:
1. I SQL Server-instansen går du till *C:\Program Files\Azure Workload Backup\bin-mappen* och skapar filen **ExtensionSettingsOverrides.json**.
2. I filen **ExtensionSettingsOverrides.json** ändrar du inställningen **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5) <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Spara ändringarna. Stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?
Nej. Den här funktionen stöds inte.

## <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas.

## <a name="can-i-see-scheduled-backup-jobs-in-the-jobs-menu"></a>Kan jag se schemalagda säkerhetskopieringsjobb på menyn jobb?

Nej. Den **säkerhetskopieringsjobb** menyn visar jobbinformation på begäran, men inte schemalagda säkerhetskopieringsjobb. Om vissa schemalagda säkerhetskopieringsjobb misslyckas finns information i aviseringarna för misslyckade jobb. För att övervaka alla schemalagda säkerhetskopieringsjobb och ad hoc-säkerhetskopieringsjobb använder du [SQL Server Management Studio](manage-monitor-sql-database-backup.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Framtida databaser automatiskt läggs till för säkerhetskopiering?

Nej. Om du väljer alternativet för servernivå när du konfigurerar skydd för en SQL Server-instans läggs alla databaser till. Om du lägger till databaser i en SQL Server-instans när du har konfigurerat skydd måste du manuellt lägga till nya databaser för att skydda dem. Databaserna inkluderas inte automatiskt i det konfigurerade skyddet.

##  <a name="how-do-i-restart-protection-after-changing-recovery-type"></a>Hur startar jag skydd när du har ändrat typ av återställning?

Utlös en fullständig säkerhetskopia. Loggsäkerhetskopior börjar som förväntat.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan jag skydda Availability Groups with system på plats?

Nej. Azure Backup skyddar SQL-servrar som körs i Azure. Om en tillgänglighetsgrupp (AG, Availability Group) fördelas mellan Azure och lokala datorer kan AG endast skyddas om den primära repliken körs i Azure. Azure Backup skyddar dessutom endast de noder som körs i samma Azure-region som Recovery Services-valvet.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan jag skydda Tillgänglighetsgrupper i olika regioner?

Azure Backup Recovery Services-valv kan identifiera och skydda alla noder i samma region som Recovery Services-valvet. Om du har en SQL AlwaysOn-tillgänglighetsgrupp som täcker flera Azure-regioner kan du behöva konfigurera säkerhetskopiering från den region där den primära noden finns. Azure Backup kommer att kunna identifiera och skydda alla databaser i tillgänglighetsgruppen enligt inställningen för säkerhetskopiering. Om inställningen för säkerhetskopiering inte uppfylls misslyckas säkerhetskopieringarna, och du får felaviseringen.

## <a name="can-i-exclude-databases-with-auto-protection-enabled"></a>Kan jag utesluta databaser med automatiskt skydd aktiverat?

Nej, [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) gäller för hela instansen. Du kan inte skydda bara vissa databaser en instans med automatiskt skydd.

## <a name="can-i-have-different-policies-in-an-auto-protected-instance"></a>Kan jag ha olika principer i en automatisk-skyddad instans?

Om du redan har några skyddade databaser i en instans fortsätter de att skyddas med sina respektive principer även när du slår **PÅ** alternativet för [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection). Alla oskyddade databaser samt de som du vill lägga till i framtiden får dock bara en enda policy som du definierar under **Konfigurera säkerhetskopiering** när databaserna har valts. Till skillnad från andra skyddade databaser kan du inte ens ändra policyn för en databas i en instans med automatiskt skydd.
Det enda sättet att åstadkomma det är att för tillfället inaktivera automatiskt skydd i instansen och sedan ändra policyn för den databasen. Du kan då återaktivera automatiskt skydd för den här instansen.

## <a name="if-i-delete-a-database-from-auto-protection-will-backups-stop"></a>Om jag tar bort en databas från automatiskt skydd kommer säkerhetskopieringar att sluta?

Nej. Om en databas släpps från en instans med automatiskt skydd ger fortfarande försök att säkerhetskopiera den databasen. Det här innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande behandlas som skyddad.

Det enda sättet att sluta skydda den här databasen är att inaktivera [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) i instansen för tillfället och sedan välja alternativet **Avbryt säkerhetskopiering** i **Säkerhetskopieringsobjekt** för den databasen. Du kan då återaktivera automatiskt skydd för den här instansen.

##  <a name="why-cant-i-see-an-added-database-for-an-auto-protected-instance"></a>Varför kan jag inte se en databas som har lagts till för en automatisk-skyddad instans?

Du kan inte se en nyligen tillagd databas till en [automatiskt skydd](backup-azure-sql-database.md#enable-auto-protection) instans som skyddas direkt skyddade objekt. Det beror på att identifieringen vanligtvis körs var 8:e timme. Användaren kan dock köra en manuell identifiering med hjälp alternativen för **Recover DBs** (Återställ databaser) för att identifiera och skydda nya databaser omedelbart såsom bilden nedan visar:

  ![Visa nyligen tillagd databas](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du](backup-azure-sql-database.md) säkerhetskopiera SQL Server-databas som körs på en virtuell Azure-dator.
