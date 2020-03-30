---
title: Om SAP HANA-databassäkerhetskopiering i virtuella Azure-datorer
description: I den här artikeln kan du läsa mer om säkerhetskopiering av SAP HANA-databaser som körs på virtuella Azure-datorer.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 52c235c95cea73a0c51c62fcb55f7f711d2eff21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476465"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Om SAP HANA-databassäkerhetskopiering i virtuella Azure-datorer

SAP HANA-databaser är verksamhetskritiska arbetsbelastningar som kräver ett mål för låg återställningspunkt (RPO) och ett snabbt återställningstidsmål (RTO). Du kan nu [säkerhetskopiera SAP HANA-databaser som körs på virtuella Azure-datorer](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db) med [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Azure Backup är [Backint-certifierad](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) av SAP, för att tillhandahålla inbyggt säkerhetskopieringsstöd genom att utnyttja SAP HANA:s inbyggda API:er. Det här erbjudandet från Azure Backup stämmer överens med Azure Backups mantra av säkerhetskopiering av **noll infrastruktur,** vilket eliminerar behovet av att distribuera och hantera infrastruktur för säkerhetskopiering. Du kan nu sömlöst säkerhetskopiera och återställa SAP HANA-databaser som körs på virtuella Azure-datorer[(virtuella datorer i M-serien](../virtual-machines/m-series.md) stöds också nu!) och utnyttja funktioner för företagshantering som Azure Backup tillhandahåller.

## <a name="added-value"></a>Mervärde

Om du använder Azure Backup för att säkerhetskopiera och återställa SAP HANA-databaser ger följande fördelar:

* **15-minuters återställningspunkt mål (RPO)**: Återvinning av kritiska data på upp till 15 minuter är nu möjligt.
* **En-klick, point-in-time återställningar:** Återställning av produktionsdata till alternativa HANA-servrar är enkelt. Kedja av säkerhetskopior och kataloger för att utföra återställningar hanteras av Azure bakom kulisserna.
* **Långsiktig lagring**: För rigorösa efterlevnads- och revisionsbehov. Behåll dina säkerhetskopior i flera år, baserat på kvarhållningstiden, bortom vilken återställningspunkterna kommer att beskäras automatiskt av den inbyggda livscykelhanteringskapaciteten.
* **Säkerhetskopieringshantering från Azure:** Använd hanterings- och övervakningsfunktionerna för Azure Backup för förbättrad hanteringsupplevelse. Azure CLI stöds också.

Om du vill visa de scenarier för säkerhetskopiering och återställning som vi stöder idag läser du [supportmatrisen FÖR SAP HANA-scenariot](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support).

## <a name="backup-architecture"></a>Säkerhetskopieringsarkitektur

![Diagram över säkerhetskopieringsarkitektur](./media/sap-hana-db-about/backup-architecture.png)

* Säkerhetskopieringsprocessen börjar [med att skapa ett recovery-tjänstvalv](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-service-vault) i Azure. Det här valvet används för att lagra säkerhetskopior och återställningspunkter som skapats över tid.
* Den Azure VM som kör SAP HANA-servern är registrerad med valvet och de databaser som ska säkerhetskopieras [identifieras](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases). För att Azure Backup-tjänsten ska kunna identifiera databaser måste ett [förregistreringsskript](https://aka.ms/scriptforpermsonhana) köras på HANA-servern som en rotanvändare.
* Det här skriptet skapar **AZUREWLBACKUPHANAUSER** DB-användare och en motsvarande nyckel med samma namn i **hdbuserstore**. Se avsnittet [Vad förregistreringsskriptet gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) för att förstå mer om vad skriptet gör.
* Azure Backup Service installerar nu **Azure Backup Plugin för HANA** på den registrerade SAP HANA-servern.
* **AzureWLBACKUPHANAUSER** DB-användaren som skapats av förregistreringsskriptet används av **Azure Backup Plugin för HANA för** att utföra alla säkerhetskopierings- och återställningsåtgärder. Om du försöker konfigurera säkerhetskopiering för SAP HANA-DB:er utan att köra skriptet kan följande felmeddelande visas: **UserErrorHanaScriptNotRun**.
* Om du vill [konfigurera säkerhetskopiering](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#configure-backup) på de databaser som identifieras väljer du den nödvändiga säkerhetskopieringsprincipen och aktiverar säkerhetskopior.

* När säkerhetskopieringen har konfigurerats konfigureras konfigurerar Azure Backup-tjänsten följande Backint-parametrar på DATABAS-nivå på den skyddade SAP HANA-servern:
  * [catalog_backup_using_backint:true]
  * [enable_accumulated_catalog_backup:falskt]
  * [parallel_data_backup_backint_channels:1]
  * [log_backup_timeout_s:900)]
  * [backint_response_timeout:7200]

>[!NOTE]
>Se till att dessa parametrar *inte* finns på VÄRD-nivå. Parametrar på värdnivå åsidosätter dessa parametrar och kan orsaka oväntat beteende.
>

* **Azure Backup Plugin för HANA** underhåller alla säkerhetskopieringsscheman och principdetaljer. Den utlöser schemalagda säkerhetskopior och kommunicerar med **HANA Backup Engine** via Backint API:erna.
* **Hana-reservmotorn** returnerar en Backint-ström med de data som ska säkerhetskopieras.
* Alla schemalagda säkerhetskopior och säkerhetskopieringar på begäran (som utlöses från Azure-portalen) som antingen är fullständiga eller differentiella initieras av **Azure Backup Plugin för HANA**. Loggsäkerhetskopior hanteras och utlöses dock av **HANA Backup Engine** själv.
* Azure Backup för SAP HANA, som är en BackInt-certifierad lösning, beror inte på underliggande disk- eller VM-typer. Säkerhetskopian utförs av strömmar som genereras av HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Använda Azure VM-säkerhetskopiering med Azure SAP HANA-säkerhetskopia

Förutom att använda SAP HANA-säkerhetskopian i Azure som tillhandahåller säkerhetskopiering och återställning på databasnivå kan du använda azure VM-säkerhetskopieringslösningen för att säkerhetskopiera operativsystem och diskar som inte är databas.

[Backint-certifierad Azure SAP HANA-säkerhetskopieringslösning](#backup-architecture) kan användas för säkerhetskopiering och återställning av databaser.

[Azure VM-säkerhetskopiering](backup-azure-vms-introduction.md) kan användas för att säkerhetskopiera operativsystemet och andra diskar som inte är databas. Den VM backup tas en gång varje dag och det säkerhetskopierar alla diskar (utom **Write Accelerator (WA) diskar** och **UltraDisks**). Eftersom databasen säkerhetskopieras med hjälp av Azure SAP HANA-säkerhetskopieringslösningen kan du bara göra en filkonsekvent säkerhetskopiering av operativsystem och icke-databasdiskar med hjälp av funktionen exkluderar disk, som för närvarande förhandsgranskas.

>[!NOTE]
> Om du använder skript före inlägg med säkerhetskopian av Azure VM tillåts appkonsekventa säkerhetskopior av databasens datavolymer. Men om loggområdet finns på WA-diskar, tar en ögonblicksbild av dessa diskar kanske inte garanterar en loggområde konsekvens. HANA har ett uttryckligt sätt att generera loggsäkerhetskopior av just denna anledning. Aktivera samma i din SAP HANA och de kan säkerhetskopieras med Azure SAP HANA-säkerhetskopiering.

Så här återställer du en virtuell dator som kör SAP HANA:

* [Återställa en ny virtuell dator från Azure VM-säkerhetskopiering](backup-azure-arm-restore-vms.md) från den senaste återställningspunkten. Eller skapa en ny tom virtuell dator och koppla diskarna från den senaste återställningspunkten.
* Eftersom WA-diskar inte säkerhetskopieras återställs de inte. Skapa tomma WA-diskar och loggområde.
* När alla andra konfigurationer (till exempel IP, systemnamn och så vidare) har angetts, är den virtuella datorn inställd på att ta emot DB-data från Azure-säkerhetskopiering.
* Nu återställa DB till den virtuella datorn från [Azure SAP HANA DB backup](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) till önskad point-in-time.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [återställer en SAP HANA-databas som körs på en Virtuell Azure-dator](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Lär dig hur du [hanterar SAP HANA-databaser som säkerhetskopieras med Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
