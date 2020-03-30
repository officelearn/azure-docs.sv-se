---
title: Vanliga frågor och svar – Säkerhetskopiering av SQL Server-databaser på virtuella Azure-datorer
description: Hitta svar på vanliga frågor om säkerhetskopiering av SQL Server-databaser på virtuella Azure-datorer med Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247714"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Vanliga frågor och svar om SQL Server-databaser som körs på en Azure VM-säkerhetskopia

Den här artikeln besvarar vanliga frågor om säkerhetskopiering av SQL Server-databaser som körs på virtuella Azure-datorer (VMs) och använder [Azure Backup-tjänsten.](backup-overview.md)

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Kan jag använda Azure backup för IaaS VM samt SQL Server på samma dator?

Ja, du kan ha både VM-säkerhetskopiering och SQL-säkerhetskopiering på samma virtuella dator. I det här fallet utlöser vi internt fullständig säkerhetskopia av endast kopia på den virtuella datorn för att inte trunkera loggarna.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Har lösningen försök igen eller automatiskt läka säkerhetskopior?

Under vissa omständigheter utlöser Azure Backup-tjänsten säkerhetskopieringar. Auto-heal kan hända för någon av de sex villkor som nämns nedan:

- Om logg- eller differentiell säkerhetskopiering misslyckas på grund av LSN-valideringsfel konverteras i stället nästa logg eller differentiell säkerhetskopiering till en fullständig säkerhetskopia.
- Om ingen fullständig säkerhetskopiering har skett före en logg eller differentiell säkerhetskopiering konverteras loggen eller differentiell säkerhetskopiering i stället till en fullständig säkerhetskopia.
- Om den senaste fullständiga säkerhetskopieringens point-in-time är äldre än 15 dagar konverteras i stället nästa logg eller differentiell säkerhetskopiering till en fullständig säkerhetskopia.
- Alla säkerhetskopieringsjobb som avbryts på grund av en tilläggsuppgradering återutlösas när uppgraderingen har slutförts och tillägget har startats.
- Om du väljer att skriva över databasen under Återställning misslyckas nästa log/differential-säkerhetskopiering och en fullständig säkerhetskopiering utlöses i stället.
- I de fall där en fullständig säkerhetskopiering krävs för att återställa loggkedjorna på grund av ändring i databasåterställningsmodellen, utlöses en fullständig automatiskt enligt nästa schema.

Automatiskt läka som en funktion är aktiverad för alla användare som standard; Men om du väljer att välja bort det, sedan utföra nedan:

- I mappen *C:\Program Files\Azure Workload Backup\bin* i SQL Server skapar eller redigerar du filen **ExtensionSettingsOverrides.json.**
- I **ExtensionSettingsOverrides.json**anger du *{"EnableAutoHealer": false}*.
- Spara ändringarna och stäng filen.
- Öppna **Uppgiftshantering** i SQL Server-instansen och starta sedan om **AzureWLBackupCoordinatorSvc-tjänsten.**

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Kan jag styra hur många samtidiga säkerhetskopior som körs på SQL-servern?

Ja. Du kan begränsa hastigheten med vilken säkerhetskopieringsprincipen körs för att minimera påverkan på en SQL Server-instans. Så här ändrar du inställningen:

1. Skapa filen *ExtensionSettingsOverrides.json* i mappen *C:\Program\Program\Azure Workload Backup\bin* i SQL Server-instansen.
2. I filen *ExtensionSettingsOverrides.json* ändrar du inställningen **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Standardvärdet för DefaultBackupTasksThreshold är **20**.

3. Spara ändringarna och stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Även om den här metoden hjälper om säkerhetskopieringsprogrammet förbrukar en stor mängd resurser, är SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) ett mer allmänt sätt att ange gränser för mängden CPU, fysisk IO och minne som inkommande programbegäranden kan använda.

> [!NOTE]
> I UX kan du fortfarande gå vidare och schemalägga så många säkerhetskopior vid varje given tidpunkt, men de kommer att bearbetas i ett glidande fönster med något att säga, 5, enligt exemplet ovan.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?

Enligt SQL-begränsningar kan du köra kopiera endast fullständig säkerhetskopiering på sekundär replik. Det är dock inte tillåtet att göra fullständig säkerhetskopiering.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan jag skydda tillgänglighetsgrupper lokalt?

Nej. Azure Backup skyddar SQL Server-databaser som körs i Azure. Om en tillgänglighetsgrupp (AG) sprids mellan Azure och lokala datorer kan AG skyddas endast om den primära repliken körs i Azure. Azure Backup skyddar också endast noder som körs i samma Azure-region som Recovery Services-valvet.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan jag skydda tillgänglighetsgrupper i olika regioner?

Azure Backup Recovery Services-valvet kan identifiera och skydda alla noder som finns i samma region som valvet. Om din SQL Server Always On-tillgänglighetsgrupp sträcker sig över flera Azure-regioner konfigurerar du säkerhetskopian från den region som har den primära noden. Azure Backup kan identifiera och skydda alla databaser i tillgänglighetsgruppen enligt dina inställningar för säkerhetskopiering. När inställningen för säkerhetskopiering inte uppfylls misslyckas säkerhetskopieringarna och du får felvarningen.

## <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas. Detaljerat beteende för portalaviseringar dokumenteras [här](backup-azure-monitoring-built-in-monitor.md). Men om du är intresserad har aviseringar även för framgångsrika jobb, kan du använda [Övervakning med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan jag se schemalagda säkerhetskopieringsjobb på menyn Säkerhetskopieringsjobb?

Menyn **Säkerhetskopieringsjobb** visar bara säkerhetskopieringsjobb på begäran. För schemalagd jobb använd [Övervakning med Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Läggs framtida databaser automatiskt till för säkerhetskopiering?

Ja, du kan uppnå den här funktionen med [automatiskt skydd](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Vad händer med säkerhetskopiorna om jag tar bort en databas från en instans med automatiskt skydd?

Om en databas tas bort från en autoskyddad instans görs fortfarande säkerhetskopiering av databasen. Detta innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande är skyddad.

Det korrekta sättet att sluta skydda den här databasen är att göra **Stoppa säkerhetskopiering** med **ta bort data** på den här databasen.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Om jag stoppar säkerhetskopiering av en databas med autoskyddat, vad kommer det att fungera?

Om du **stoppar säkerhetskopiering med lagringsdata**kommer inga framtida säkerhetskopior att äga rum och de befintliga återställningspunkterna förblir intakta. Databasen betraktas fortfarande som skyddad och visas under **säkerhetskopieringsobjekten**.

Om du **stoppar säkerhetskopiering med ta bort data**kommer inga framtida säkerhetskopior att äga rum och de befintliga återställningspunkterna tas också bort. Databasen betraktas som oskyddad och visas under instansen i konfigurera säkerhetskopiering. Men till skillnad från andra upp-skyddade databaser som kan väljas manuellt eller som kan få autoskyddad, verkar den här databasen nedtonad och kan inte väljas. Det enda sättet att skydda databasen igen är att inaktivera automatiskt skydd på instansen. Du kan nu välja den här databasen och konfigurera skyddet på den eller återaktivera automatiskt skydd för instansen igen.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Om jag ändrar namnet på databasen efter att den har skyddats, vad blir då beteendet?

En omdöpt databas behandlas som en ny databas. Därför kommer tjänsten att behandla denna situation som om databasen inte hittades och med misslyckas säkerhetskopior.

Du kan välja databasen, som nu har bytt namn och konfigurera skyddet på den. Om det automatiska skyddet är aktiverat för instansen identifieras och skyddas den omdöpta databasen automatiskt.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Varför kan jag inte se en tillagd databas för en autoskyddad instans?

En databas som du [lägger till i en autoskyddad instans](backup-sql-server-database-azure-vms.md#enable-auto-protection) kanske inte omedelbart visas under skyddade objekt. Det beror på att identifieringen vanligtvis körs var 8:e timme. Du kan dock identifiera och skydda nya databaser omedelbart om du kör en identifiering manuellt genom att välja **Återupptäcka DBs**, som visas i följande bild:

  ![Upptäck en nyligen tillagd databas manuellt](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar en SQL Server-databas](backup-azure-sql-database.md) som körs på en Virtuell Azure.Learn how to back up a SQL Server database that's running on an Azure VM.
