---
title: Felsöka fel vid säkerhets kopiering av SAP HANA databaser
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 9c6e4c66d96b02c2d5b4b4fe70fe6e6798c4e2c6
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285920"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer. Mer information om de SAP HANA säkerhets kopierings scenarier som vi för närvarande stöder finns i [scenario support](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Krav och behörigheter

Se [nödvändiga komponenter](tutorial-backup-sap-hana-db.md#prerequisites) och konfigurera [behörigheter](tutorial-backup-sap-hana-db.md#setting-up-permissions) innan du konfigurerar säkerhets kopieringar.

## <a name="common-user-errors"></a>Vanliga användar fel

| Fel                                | Felmeddelande                    | Möjliga orsaker                                              | Rekommenderad åtgärd                                           |
| ------------------------------------ | -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| UserErrorInOpeningHanaOdbcConnection | Det gick inte att ansluta till HANA-systemet | SAP HANA-instansen kan vara avstängd. <br> De behörigheter som krävs för Azure Backup för att interagera med HANA-databasen har inte angetts. | Kontrol lera om SAP HANA databasen är igång. Om databasen är igång kontrollerar du om alla nödvändiga behörigheter är inställda. Om någon av behörigheterna saknas kör du för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) för att lägga till de behörigheter som saknas. |
| UserErrorHanaInstanceNameInvalid | Den angivna SAP HANA-instansen är antingen ogiltig eller kan inte hittas | Flera SAP HANA-instanser på en enskild virtuell Azure-dator kan inte säkerhets kopie ras. | Kör för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) på den SAP HANA-instans som du vill säkerhetskopiera. Kontakta Microsoft-supporten om problemet kvarstår. |
| UserErrorHanaUnsupportedOperation | Den angivna SAP HANA åtgärden stöds inte | Azure Backup för SAP HANA stöder inte stegvis säkerhets kopiering och åtgärder som utförs på SAP HANA ursprungliga klienter (Studio/cockpit/DBA-cockpit) | Mer information finns [här](sap-hana-backup-support-matrix.md#scenario-support). |
| UserErrorHANAPODoesNotSupportBackupType | Den här SAP HANA databasen har inte stöd för den begärda säkerhets kopierings typen | Azure Backup stöder inte stegvis säkerhets kopiering och säkerhets kopiering med ögonblicks bilder | Mer information finns [här](sap-hana-backup-support-matrix.md#scenario-support). |
| UserErrorHANALSNValidationFailure | Logg kedjan för säkerhets kopiering är bruten | Mål platsen för säkerhets kopieringen kan ha uppdaterats från backint till fil systemet, eller så har den körbara filen backint ändrats | Utlös en fullständig säkerhets kopiering för att lösa problemet |
| UserErrorIncomaptibleSrcTargetSystsemsForRestore | Käll-och mål systemen för återställning är inkompatibla | Mål systemet för återställning är inte kompatibelt med källan | Se SAP NOTE [1642148](https://launchpad.support.sap.com/#/notes/1642148) för mer information om de återställnings typer som stöds idag |
| UserErrorSDCtoMDCUpgradeDetected | SDC till MDC-uppgradering upptäcktes | SAP HANA-instansen har uppgraderats från SDC till MDC. Säkerhets kopieringar kommer att Miss lyckas efter uppdateringen. | Följ stegen som beskrivs i [avsnittet Uppgradera från SAP HANA 1,0 till 2,0](#upgrading-from-sap-hana-10-to-20) för att lösa problemet |
| UserErrorInvalidBackintConfiguration | En ogiltig backint-konfiguration upptäcktes | De sekundära parametrarna har angetts felaktigt för Azure Backup | Kontrol lera om följande (backint) parametrar har angetts: <br> * [catalog_backup_using_backint: sant] <br>  * [enable_accumulated_catalog_backup: falskt] <br> * [parallel_data_backup_backint_channels: 1] <br>* [log_backup_timeout_s: 900)] <br> * [backint_response_timeout: 7200] <br> Om det finns backint-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra [stoppa skyddet och behålla säkerhets kopierings data](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) från Azure Portal och sedan välja **återuppta säkerhets kopiering**. |

## <a name="restore-checks"></a>Återställnings kontroller

### <a name="single-container-database-sdc-restore"></a>Återställning av en SDC-databas (Single container Database)

Ta hand om indata när du återställer en enda behållar databas (SDC) för HANA till en annan SDC-dator. Databas namnet måste anges med gemener och "SDC" har lagts till inom hakparenteser. HANA-instansen kommer att visas med versaler.

Anta att en SDC HANA-instans "H21" säkerhets kopie ras. På sidan säkerhets kopierings objekt visas namnet **"H21 (SDC)"** för säkerhets kopierings objekt. Om du försöker återställa databasen till en annan mål SDC, säg H11, måste du ange följande indata.

![SDC Återställ indata](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande punkter:

- Som standard fylls det återställda DB-namnet på med namnet för säkerhets kopian, t. ex. H21 (SDC)
- Om du väljer målet som H11 ändras inte det återställda databas namnet automatiskt. **Den bör redige ras till H11 (SDC)** . För SDC kommer det återställda databas namnet att vara mål instans-ID: t med gemener och "SDC" i hakparenteser.
- Eftersom SDC bara kan ha en enda databas, måste du också klicka på kryss rutan för att tillåta åsidosättning av befintliga databas data med återställnings punkt data.
- Linux är Skift läges känsligt. Var därför noga med att bevara ärendet.

### <a name="multiple-container-database-mdc-restore"></a>Återställning av MDC (Multiple container Database)

I flera container-databaser för HANA är standard konfigurationen SYSTEMDB + 1 eller flera klient-databaser. Att återställa en hel SAP HANA instans innebär att återställa både SYSTEMDB-och klient databaser. En återställer SYSTEMDB först och fortsätter sedan för klient organisations databasen. System DB innebär i princip att åsidosätta system informationen på det valda målet. Den här återställningen åsidosätter också BackInt-relaterad information i mål instansen. När system databasen har återställts till en mål instans måste därför en köra skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Uppgraderar från SAP HANA 1,0 till 2,0

Om du skyddar SAP HANA 1,0-databaser och vill uppgradera till 2,0 utför du stegen som beskrivs nedan:

- [Stoppa skyddet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med Behåll data för den gamla SDC-databasen.
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) med rätt information om (sid och MDC).
- Omregistrera tillägg (säkerhets kopiering > Visa information – > väljer den relevanta Azure-> Omregistrera).
- Klicka på identifiera om databaser för samma virtuella dator. Den här åtgärden ska visa den nya databaser i steg 2 med rätt information (SYSTEMDB och klient organisations databasen, inte SDC).
- Skydda dessa nya databaser.

## <a name="upgrading-without-an-sid-change"></a>Uppgradera utan en SID-ändring

Uppgraderingar av OS eller SAP HANA som inte orsakar en SID ändring kan hanteras enligt beskrivningen nedan:

- [Stoppa skyddet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med Behåll data för databasen
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) igen
- [Återuppta skyddet](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) av databasen igen

## <a name="next-steps"></a>Nästa steg

- Läs [vanliga frågor](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer]
