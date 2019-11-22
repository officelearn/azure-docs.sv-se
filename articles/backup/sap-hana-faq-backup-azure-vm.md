---
title: Vanliga frågor och svar – säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer
description: I den här artikeln hittar du svar på vanliga frågor om hur du säkerhetskopierar SAP HANA databaser med hjälp av tjänsten Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287845"
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

### <a name="are-future-databases-automatically-added-for-backup"></a>Läggs framtida databaser automatiskt till för säkerhets kopiering?

Nej, detta stöds inte för närvarande.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Vad händer med säkerhets kopior om jag tar bort en databas från en instans?

Om en databas bryts från en SAP HANA instans, görs fortfarande databas säkerhets kopiorna. Detta innebär att den borttagna databasen börjar visas som ohälsosam under **säkerhets kopierings objekt** och fortfarande är skyddad.
Det korrekta sättet att sluta skydda den här databasen är att **stoppa säkerhets kopieringen med ta bort data** på den här databasen.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Vad händer om jag ändrar namnet på databasen efter att det har skyddats?

En döpt databas behandlas som en ny databas. Tjänsten kommer därför att behandla den här situationen som om databasen inte hittades och att säkerhets kopieringen Miss lyckas. Den omdöpta databasen visas som en ny databas och måste konfigureras för skydd.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Vilka är kraven för att säkerhetskopiera SAP HANA databaser på en virtuell Azure-dator?

Se avsnittet [krav](tutorial-backup-sap-hana-db.md#prerequisites) och [konfiguration av behörigheter](tutorial-backup-sap-hana-db.md#setting-up-permissions) .

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Vilka behörigheter ska ställas in för Azure för att kunna säkerhetskopiera SAP HANA databaser?

Genom att köra skriptet före registreringen anger du de behörigheter som krävs för att Azure ska kunna säkerhetskopiera SAP HANA-databaser. Du hittar mer information om behörigheterna [här](tutorial-backup-sap-hana-db.md#setting-up-permissions).

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Kommer säkerhets kopieringar att fungera efter migrering av SAP HANA från 1,0 till 2,0?

Se [det här avsnittet](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) i fel söknings guiden.

## <a name="restore"></a>Återställ

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Varför kan jag inte se det HANA-system jag vill att min databas ska återställas till?

Kontrol lera om alla krav för att återställa till mål SAP HANA instansen är uppfyllda. Mer information finns i [krav-Restore SAP HANA-databaser i Azure VM](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Varför går det inte att återställa Skriv över DB för min databas?

Se till att alternativet **Framtvinga överskrivning** är valt vid återställning.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Varför visas fel meddelandet "käll-och mål system för återställning är inkompatibla"?

Läs SAP HANA anmärkning [1642148](https://launchpad.support.sap.com/#/notes/1642148) för att se vilka återställnings typer som stöds för närvarande.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar SAP HANA databaser](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) som körs på virtuella Azure-datorer.
