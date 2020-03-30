---
title: Vanliga frågor och svar – Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer
description: I den här artikeln kan du hitta svar på vanliga frågor om säkerhetskopiering av SAP HANA-databaser med hjälp av Azure Backup-tjänsten.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155400"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Vanliga frågor och svar – Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer

Den här artikeln svarar på vanliga frågor om säkerhetskopiering av SAP HANA-databaser med hjälp av Azure Backup-tjänsten.

## <a name="backup"></a>Säkerhetskopiering

### <a name="how-many-full-backups-are-supported-per-day"></a>Hur många fullständiga säkerhetskopieringar stöds per dag?

Vi stöder endast en fullständig säkerhetskopiering per dag. Du kan inte ha differentiell säkerhetskopiering och fullständig säkerhetskopiering som utlöses samma dag.

### <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas. Detaljerat beteende för portalaviseringar dokumenteras [här](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Men om du är intresserad av att ha aviseringar även för lyckade jobb kan du använda [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan jag se schemalagda säkerhetskopieringsjobb på menyn Säkerhetskopieringsjobb?

Menyn Säkerhetskopieringsjobb visar bara ad hoc-säkerhetskopieringsjobb. För schemalagda jobb använder du [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Läggs framtida databaser automatiskt till för säkerhetskopiering?

Nej, detta stöds för närvarande inte.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Om jag tar bort en databas från en instans, vad händer med säkerhetskopiorna?

Om en databas tas bort från en SAP HANA-instans görs fortfarande försök att säkerhetskopiera databasen. Detta innebär att den borttagna databasen börjar visas som felaktig under **Säkerhetskopieringsobjekt** och fortfarande är skyddad.
Det korrekta sättet att sluta skydda databasen är att utföra **Stoppa säkerhetskopiering med ta bort data** på den här databasen.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Om jag ändrar namnet på databasen efter att den har skyddats, vad blir då beteendet?

En omdöpt databas behandlas som en ny databas. Därför kommer tjänsten att behandla den här situationen som om databasen inte hittades och med misslyckas säkerhetskopior. Den omdöpta databasen visas som en ny databas och måste konfigureras för skydd.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Vilka är förutsättningarna för att säkerhetskopiera SAP HANA-databaser på en Virtuell Azure??

Se [förutsättningarna](tutorial-backup-sap-hana-db.md#prerequisites) och [vad förregistreringsskriptet gör.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Vilka behörigheter bör anges för att Azure ska kunna säkerhetskopiera SAP HANA-databaser?

Köra skriptet för förhandsregistrering anger de behörigheter som krävs för att azure ska kunna säkerhetskopiera SAP HANA-databaser. Du kan hitta mer vad pre-registrering skriptet gör [här](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Kommer säkerhetskopior att fungera efter att ha migrerat SAP HANA från 1.0 till 2.0?

Se [det här avsnittet](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) i felsökningsguiden.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Kan Azure HANA Backup ställas in mot en virtuell IP (belastningsutjämnare) och inte en virtuell dator?

För närvarande har vi inte möjlighet att ställa in lösningen mot en virtuell IP ensam. Vi behöver en virtuell maskin för att utföra lösningen.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Jag har en SAP HANA System Replication (HSR), hur ska jag konfigurera säkerhetskopiering för den här installationen?

De primära och sekundära noderna i HSR kommer att behandlas som två enskilda, un-relaterade virtuella datorer. Du måste konfigurera säkerhetskopiering på den primära noden och när redundansen sker måste du konfigurera säkerhetskopiering på den sekundära noden (som nu blir den primära noden). Det finns ingen automatisk "fail-over" av säkerhetskopiering till den andra noden.

## <a name="restore"></a>Återställ

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Varför kan jag inte se det HANA-system som jag vill att databasen ska återställas till?

Kontrollera om alla förutsättningar för återställningen till målet SAP HANA-instansen är uppfyllda. Mer information finns i [Förutsättningar - Återställ SAP HANA-databaser i Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Varför misslyckas överskrivningen av DB-återställningen för min databas?

Kontrollera att alternativet **Force Overwrite** är markerat när du återställer.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Varför ser jag felet "Käll- och målsystem för återställning är inkompatibla"?

Se SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) för att se vilka återställningstyper som för närvarande stöds.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar SAP HANA-databaser](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) som körs på virtuella Azure-datorer.
