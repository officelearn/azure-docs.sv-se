---
title: Vanliga frågor och svar om säkerhetskopiering av SQL Server-databaser på Azure Virtual Machines med Azure Backup
description: Hitta svar på vanliga frågor om säkerhetskopiering av SQL Server-databaser på virtuella Azure-datorer med Azure Backup.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: sachdevaswati
ms.openlocfilehash: 48a0400a471e06f65c1d548b7c1c419a1cb198bd
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58284586"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Vanliga frågor och svar om SQL Server-databaser som körs på en virtuell Azure-säkerhetskopiering

Den här artikeln innehåller vanliga frågor och svar om hur du säkerhetskopierar SQL Server-databaser som kör på Azure virtual machines (VMs) och som använder den [Azure Backup](backup-overview.md) service.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Varken lösningen försök eller automatisk-reparation av nodtjänst säkerhetskopiorna?

Under vissa omständigheter utlöser korrigerande säkerhetskopieringar i Azure Backup-tjänsten. Reparation av automatisk-nodtjänst kan bero på något av de sex villkor som nämns nedan:

  - Om eller den differentiella säkerhetskopian misslyckas på grund av verifieringsfel för LSN konverteras i stället nästa eller den differentiella säkerhetskopian till en fullständig säkerhetskopia.
  - Om ingen fullständig säkerhetskopiering har hänt innan en eller den differentiella säkerhetskopian konverteras i stället den eller den differentiella säkerhetskopian till en fullständig säkerhetskopia.
  - Om den senaste fullständiga säkerhetskopian point-in-time är äldre än 15 dagar, konverteras i stället nästa loggen eller differentiella säkerhetskopian till en fullständig säkerhetskopia.
  - Alla säkerhetskopieringsjobb som annulleras på grund av en uppgradering på tillägget utlöses igen när uppgraderingen är klar och tillägget startas.
  - Om du vill skriva över databasen under återställning nästa logg/differentiell säkerhetskopiering misslyckas och en fullständig säkerhetskopiering utlöses i stället.
  - I fall där en fullständig säkerhetskopiering krävs för att återställa log kedjor på grund av för att ändra databasens Återställningsmodell hämtar en fullständig aktiveras automatiskt i nästa schema.

Automatisk-reparation av nodtjänst som en funktion är aktiverad för alla användare som standard. Men om du väljer att inte välja bort den utför sedan den nedan:

  * På SQL Server-instansen i den *c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin* mapp, skapa eller redigera den **ExtensionSettingsOverrides.json** fil.
  * I den **ExtensionSettingsOverrides.json**anger *{”EnableAutoHealer”: false}*.
  * Spara ändringarna och stäng filen.
  * Öppna på SQL Server-instansen **uppgift hantera** och starta sedan om den **AzureWLBackupCoordinatorSvc** service.  

## <a name="can-i-control-as-to-how-many-concurrent-backups-run-on-the-sql-server"></a>Kan jag styra vad gäller hur många samtidiga säkerhetskopieringar som körs på SQLServer?

Ja. Du kan begränsa den hastighet som principen för säkerhetskopiering körs för att minimera effekten på en SQL Server-instans. Så här ändrar du inställningen:
1. På SQL Server-instansen i den *c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin* mapp, skapa den *ExtensionSettingsOverrides.json* fil.
2. I den *ExtensionSettingsOverrides.json* filen, ändra den **DefaultBackupTasksThreshold** till ett lägre värde (till exempel 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Spara ändringarna och stäng filen.
4. I SQL Server-instansen öppnar du **Aktivitetshanteraren**. Starta om tjänsten **AzureWLBackupCoordinatorSvc**.

> [!NOTE]
> I UX-Gränssnittet du fortfarande kan gå vidare och schemalägga så många säkerhetskopior vid något tillfälle, behandlas men de kommer i ett skjutfönster på säga 5, enligt exemplet ovan.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Kan jag köra en fullständig säkerhetskopia från en sekundär replik?
Enligt SQL-begränsningar, kan du köra Kopiera bara fullständig säkerhetskopiering på den sekundära repliken; men fullständig säkerhetskopiering stöds inte.

## <a name="can-i-protect-availability-groups-on-premises"></a>Kan jag skydda tillgänglighet grupper lokalt?
Nej. Azure Backup skyddar SQL Server-databaser som körs i Azure. Om en tillgänglighetsgrupp (AG) fördelas mellan Azure och lokala datorer, kan AG skyddas endast om den primära repliken körs i Azure. Azure Backup skyddar dessutom endast de noder som körs i samma Azure-region som Recovery Services-valvet.

## <a name="can-i-protect-availability-groups-across-regions"></a>Kan jag skydda Tillgänglighetsgrupper i olika regioner?
Azure Backup Recovery Services-valvet kan identifiera och skydda alla noder som finns i samma region som valvet. Om din SQL Server Always On availability group sträcker sig över flera Azure-regioner, Ställ in säkerhetskopian från den region som har den primära noden. Azure Backup kan identifiera och skydda alla databaser i tillgänglighetsgruppen enligt din inställning för säkerhetskopiering. När din inställning för säkerhetskopiering inte uppfylls, säkerhetskopieringar misslyckas och du får felet aviseringen.

## <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?
Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas. Detaljerad beteende för portalen aviseringar dokumenteras [här](backup-azure-monitoring-built-in-monitor.md). Men om du är intresserad av har aviseringar även för genomförda jobb kan du använda [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan jag se schemalagda säkerhetskopieringsjobb på menyn säkerhetskopieringsjobb?
Den **säkerhetskopieringsjobbet** menyn visas bara adhoc-säkerhetskopieringsjobb. Schemalagt jobb använder [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Framtida databaser automatiskt läggs till för säkerhetskopiering?
Ja, du kan uppnå den här funktionen med [automatiskt skydd](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Om jag tar bort en databas från en instans av autoprotected vad händer med säkerhetskopiorna?
Om en databas har släppts från en instans av autoprotected provas fortfarande säkerhetskopior av databasen. Detta innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande är skyddad.

Det korrekta sättet att sluta skydda den här databasen är att göra **stoppa säkerhetskopiering** med **ta bort data** på den här databasen.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Om jag förhindrar säkerhetskopieringen av en databas för autoprotected vad blir sitt beteende?
Om du gör **Avbryt säkerhetskopiering med Behåll data**, inga ytterligare säkerhetskopieringar kommer att äga rum och befintliga återställningspunkter förblir intakta. Databasen fortfarande betraktas som skyddad och att visas den **Säkerhetskopiera objekt**.

Om du gör **Avbryt säkerhetskopiering med ta borttagningsdata**, inga ytterligare säkerhetskopieringar kommer att äga rum och befintliga återställningspunkter tas också bort. Databasen betraktas som icke skyddade och att visas under instansen i Konfigurera säkerhetskopieringen. Dock visas nedtonade databasen till skillnad från andra upp-skyddade databaser som kan väljas manuellt eller som kan få autoprotected, och kan inte väljas. Det enda sättet att skydda igen den här databasen är att inaktivera automatiskt skydd på instansen. Nu kan du välja den här databasen och konfigurera skydd på den eller återaktivera att automatiskt skydd på instansen.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Om jag ändrar namnet på databasen när den har skyddats, vad blir beteendet?
Ett nytt namngiven databas behandlas som en ny databas. Tjänsten kommer därför behandlar den här situationen som om databasen gick inte att hitta och med misslyckas säkerhetskopieringarna.

Du kan välja för databasen, som nu har fått nytt namn och konfigurera skydd på den. Om det automatiska skyddet aktiveras på instansen, har identifierats omdöpt databasen automatiskt och skyddas.

##  <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Varför kan jag inte se en databas som har lagts till för en autoprotected-instans?
En databas som du [lägger till i en instans av autoprotected](backup-sql-server-database-azure-vms.md#enable-auto-protection) visas inte direkt under skyddade objekt. Det beror på att identifieringen vanligtvis körs var 8:e timme. Men du kan identifiera och skydda nya databaser direkt om du kör en identifiering manuellt genom att välja **återställa databaser**, enligt följande bild.

  ![Identifiera manuellt en nyligen tillagd databas](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopiera SQL Server-databasen](backup-azure-sql-database.md) som körs på en virtuell Azure-dator.
