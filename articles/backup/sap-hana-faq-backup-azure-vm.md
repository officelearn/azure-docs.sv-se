---
title: Vanliga frågor och svar – Säkerhetskopiera SAP HANA-databaser på virtuella Azure-datorer
description: I den här artikeln hittar du svar på vanliga frågor om hur du säkerhetskopierar SAP HANA databaser med hjälp av tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: f9e0d96439a79c2c3d2cb2caa00ff09be3ff790d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660111"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Vanliga frågor och svar – säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer

I den här artikeln besvaras vanliga frågor om att säkerhetskopiera SAP HANA-databaser med hjälp av tjänsten Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Hur många fullständiga säkerhets kopieringar stöds per dag?

Vi stöder bara en fullständig säkerhets kopiering per dag. Du kan inte ha differentiell säkerhets kopiering och fullständig säkerhets kopiering aktive rad på samma dag.

### <a name="do-successful-backup-jobs-create-alerts"></a>Skapar lyckade säkerhetskopieringsjobb aviseringar?

Nej. Lyckade säkerhetskopieringsjobb genererar inte aviseringar. Aviseringar skickas bara för säkerhetskopieringsjobb som misslyckas. Detaljerade funktioner för Portal varningar dokumenteras [här](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Men om du är intresse rad av aviseringar även för lyckade jobb kan du använda [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Kan jag se schemalagda säkerhets kopierings jobb på menyn säkerhets kopierings jobb?

Menyn säkerhets kopierings jobb visar bara ad hoc-säkerhetskopierade jobb. För schemalagda jobb använder du [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>Läggs framtida databaser automatiskt till för säkerhetskopiering?

Nej, detta stöds inte för närvarande.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Vad händer med säkerhets kopior om jag tar bort en databas från en instans?

Om en databas bryts från en SAP HANA instans, görs fortfarande databas säkerhets kopiorna. Detta innebär att den borttagna databasen börjar visas som ohälsosam under **säkerhets kopierings objekt** och fortfarande är skyddad.
Det korrekta sättet att sluta skydda den här databasen är att **stoppa säkerhets kopieringen med ta bort data** på den här databasen.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Vad händer om jag ändrar namnet på databasen efter att det har skyddats?

En döpt databas behandlas som en ny databas. Tjänsten kommer därför att behandla den här situationen som om databasen inte hittades och att säkerhets kopieringen Miss lyckas. Den omdöpta databasen visas som en ny databas och måste konfigureras för skydd.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Vilka är kraven för att säkerhetskopiera SAP HANA databaser på en virtuell Azure-dator?

Se [kraven](tutorial-backup-sap-hana-db.md#prerequisites) och [Vad skriptet gör för registrering](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Vilka behörigheter ska ställas in för Azure för att kunna säkerhetskopiera SAP HANA databaser?

Genom att köra skriptet före registreringen anger du de behörigheter som krävs för att Azure ska kunna säkerhetskopiera SAP HANA-databaser. Du hittar mer information om skriptet för för [registrering.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Kommer säkerhets kopieringar att fungera efter migrering av SAP HANA från 1,0 till 2,0?

Se [det här avsnittet](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) i fel söknings guiden.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Kan Azure HANA-säkerhetskopiering konfigureras mot en virtuell IP-adress (belastningsutjämnare) och inte en virtuell dator?

För närvarande har vi inte möjlighet att konfigurera lösningen mot en virtuell IP-adress. Vi behöver en virtuell dator för att köra lösningen.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Jag har en SAP HANA system replikering (HSR), hur ska jag konfigurera säkerhets kopiering för den här installationen?

De primära och sekundära noderna i HSR kommer att behandlas som två egna, icke-relaterade virtuella datorer. Du måste konfigurera säkerhets kopiering på den primära noden och när redundansen inträffar måste du konfigurera säkerhets kopiering på den sekundära noden (som nu blir den primära noden). Det finns ingen automatisk "redundans" av säkerhets kopian till den andra noden.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Hur kan jag flytta en säkerhets kopia på begäran till det lokala fil systemet i stället för Azure-valvet?

1. Vänta tills den pågående säkerhets kopieringen har slutförts för den önskade databasen (kontroll från Studio för slut för ande)
1. Inaktivera logg säkerhets kopior och ange katalog säkerhets kopian till **fil systemet** för den önskade databasen med hjälp av följande steg:
1. Dubbelklicka på **SYSTEMDB**  ->  **konfiguration**  ->  **Välj databas**  ->  **filter (logg)**
    1. Ange enable_auto_log_backup till **Nej**
    1. Ange log_backup_using_backint till **falskt**
1. Ta en säkerhets kopia på begäran på önskad databas och vänta tills säkerhets kopieringen och katalogen har slutförts.
1. Återgå till de tidigare inställningarna så att säkerhets kopieringar kan flöda till Azure-valvet:
    1. Ange enable_auto_log_backup till **Ja**
    1. Ange log_backup_using_backint till **Sant**

## <a name="restore"></a>Återställ

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Varför kan jag inte se det HANA-system jag vill att min databas ska återställas till?

Kontrol lera om alla krav för att återställa till mål SAP HANA instansen är uppfyllda. Mer information finns i [krav-Restore SAP HANA-databaser i Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Varför går det inte att återställa Skriv över DB för min databas?

Se till att alternativet **Framtvinga överskrivning** är valt vid återställning.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Varför visas fel meddelandet "käll-och mål system för återställning är inkompatibla"?

Läs SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148) för att se vilka återställnings typer som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar SAP HANA databaser](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) som körs på virtuella Azure-datorer.
