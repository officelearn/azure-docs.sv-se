---
title: Felsöka fel vid säkerhets kopiering av SAP HANA databaser
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e8bb1d3328f95b647a788c53afe3ac1455eefa13
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665346"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer. Mer information om de SAP HANA säkerhets kopierings scenarier som vi för närvarande stöder finns i [scenario support](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Krav och behörigheter

Se [nödvändiga komponenter](tutorial-backup-sap-hana-db.md#prerequisites) och konfigurera [behörigheter](tutorial-backup-sap-hana-db.md#setting-up-permissions) innan du konfigurerar säkerhets kopieringar.

## <a name="common-user-errors"></a>Vanliga användar fel

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Felmeddelande      | <span style="font-weight:normal">Det gick inte att ansluta till HANA-systemet</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen kan vara avstängd.<br/>De behörigheter som krävs för Azure Backup för att interagera med HANA-databasen har inte angetts. |
| **Rekommenderad åtgärd** | Kontrol lera om SAP HANA databasen är igång. Om databasen är igång kontrollerar du om alla nödvändiga behörigheter är inställda. Om någon av behörigheterna saknas kör du för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) för att lägga till de behörigheter som saknas. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA-instansen är antingen ogiltig eller kan inte hittas</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Flera SAP HANA-instanser på en enskild virtuell Azure-dator kan inte säkerhets kopie ras. |
| **Rekommenderad åtgärd** | Kör för [registrerings skriptet](https://aka.ms/scriptforpermsonhana) på den SAP HANA-instans som du vill säkerhetskopiera. Kontakta Microsoft-supporten om problemet kvarstår. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA åtgärden stöds inte</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure Backup för SAP HANA stöder inte stegvis säkerhets kopiering och åtgärder som utförs på SAP HANA ursprungliga klienter (Studio/cockpit/DBA-cockpit) |
| **Rekommenderad åtgärd** | Mer information finns [här](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Felmeddelande      | <span style="font-weight:normal">Den här SAP HANA databasen har inte stöd för den begärda säkerhets kopierings typen</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure Backup stöder inte stegvis säkerhets kopiering och säkerhets kopiering med ögonblicks bilder |
| **Rekommenderad åtgärd** | Mer information finns [här](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Felmeddelande      | <span style="font-weight:normal">Logg kedjan för säkerhets kopiering är bruten</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Mål platsen för säkerhets kopieringen kan ha uppdaterats från backint till fil systemet, eller så har den körbara filen backint ändrats |
| **Rekommenderad åtgärd** | Utlös en fullständig säkerhets kopiering för att lösa problemet                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Felmeddelande      | <span style="font-weight:normal">Käll-och mål systemen för återställning är inkompatibla</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Mål systemet för återställning är inte kompatibelt med källan |
| **Rekommenderad åtgärd** | Se SAP NOTE [1642148](https://launchpad.support.sap.com/#/notes/1642148) för mer information om de återställnings typer som stöds idag |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Felmeddelande      | <span style="font-weight:normal">SDC till MDC-uppgradering upptäcktes</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen har uppgraderats från SDC till MDC. Säkerhets kopieringar kommer att Miss lyckas efter uppdateringen. |
| **Rekommenderad åtgärd** | Följ stegen som beskrivs i [avsnittet Uppgradera från SAP HANA 1,0 till 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) för att lösa problemet |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande      | <span style="font-weight:normal">En ogiltig backint-konfiguration upptäcktes</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | De sekundära parametrarna har angetts felaktigt för Azure Backup |
| **Rekommenderad åtgärd** | Kontrol lera om följande (backint) parametrar har angetts:<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: falskt]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Om det finns backint-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra [stoppa skyddet och behålla säkerhets kopierings data](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) från Azure Portal och sedan välja **återuppta säkerhets kopiering**. |

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
