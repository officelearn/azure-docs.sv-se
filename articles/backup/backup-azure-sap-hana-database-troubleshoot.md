---
title: Felsöka fel vid säkerhets kopiering av SAP HANA databaser
description: Beskriver hur du felsöker vanliga fel som kan uppstå när du använder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 01514847dcd38842d70c4caef2e38df9df3f620a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652079"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhets kopiering av SAP HANA databaser på Azure

Den här artikeln innehåller felsöknings information för att säkerhetskopiera SAP HANA databaser på virtuella Azure-datorer. Mer information om de SAP HANA säkerhets kopierings scenarier som vi för närvarande stöder finns i [scenario support](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Krav och behörigheter

Se [kraven](tutorial-backup-sap-hana-db.md#prerequisites) och [Vad skriptet för för registrering gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) i avsnitten innan du konfigurerar säkerhets kopieringar.

## <a name="common-user-errors"></a>Vanliga användar fel

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Felmeddelande**      | <span style="font-weight:normal">Azure Backup har inte de roll behörigheter som krävs för att utföra säkerhets kopiering</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Rollen kan ha skrivits över.                          |
| **Rekommenderad åtgärd** | Lös problemet genom att köra skriptet från rutan för att **hitta databas** eller ladda ned det [här](https://aka.ms/scriptforpermsonhana). Du kan också lägga till rollen SAP_INTERNAL_HANA_SUPPORT till säkerhets kopierings användaren för arbets belastningen (AZUREWLBACKUPHANAUSER). |

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
| **Rekommenderad åtgärd** | Kontrol lera om följande (backint) parametrar har angetts:<br/>\*[catalog_backup_using_backint: sant]<br/>\*[enable_accumulated_catalog_backup: falskt]<br/>\*[parallel_data_backup_backint_channels: 1]<br/>\*[log_backup_timeout_s: 900)]<br/>\*[backint_response_timeout: 7200]<br/>Om det finns backint-baserade parametrar i värden tar du bort dem. Om parametrarna inte finns på VÄRDnivå men har ändrats manuellt på en databas nivå, återställer du dem till lämpliga värden enligt beskrivningen ovan. Du kan också köra [stoppa skyddet och behålla säkerhets kopierings data](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) från Azure Portal och sedan välja **återuppta säkerhets kopiering**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Felmeddelande  |Käll-och mål systemen för återställning är inkompatibla  |
|---------|---------|
|Möjliga orsaker   | Käll-och mål systemen som valts för återställning är inkompatibla        |
|Rekommenderad åtgärd   |   Se till att återställnings scenariot inte finns i följande lista över möjliga inkompatibla återställningar: <br><br>   **Fall 1:** SYSTEMDB kan inte byta namn under återställningen.  <br><br> **Fall 2:** Source-SDC och Target-MDC: käll databasen kan inte återställas som SYSTEMDB eller klient organisations databasen på målet. <br><br> **Fall 3:** Source-MDC och Target-SDC: det går inte att återställa käll databasen (SYSTEMDB eller klient organisations databasen) till målet. <br><br>  Mer information finns i anmärkning 1642148 i Start [fönstret för SAP-support](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Återställnings kontroller

### <a name="single-container-database-sdc-restore"></a>Återställning av en SDC-databas (Single container Database)

Ta hand om indata när du återställer en enda behållar databas (SDC) för HANA till en annan SDC-dator. Databas namnet måste anges med gemener och "SDC" har lagts till inom hakparenteser. HANA-instansen kommer att visas med versaler.

Anta att en SDC HANA-instans "H21" säkerhets kopie ras. På sidan säkerhets kopierings objekt visas namnet **"H21 (SDC)"** för säkerhets kopierings objekt. Om du försöker återställa databasen till en annan mål SDC, säg H11, måste du ange följande indata.

![Namn på återställd SDC-databas](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande punkter:

- Som standard fylls det återställda DB-namnet på med namnet på säkerhets kopie posten. I det här fallet H21 (SDC).
- Om du väljer målet som H11 ändras inte det återställda databas namnet automatiskt. **Den bör redige ras till H11 (SDC)**. För SDC kommer det återställda databas namnet att vara mål instans-ID: t med gemener och "SDC" i hakparenteser.
- Eftersom SDC bara kan ha en enda databas, måste du också klicka på kryss rutan för att tillåta åsidosättning av befintliga databas data med återställnings punkt data.
- Linux är Skift läges känsligt. Var noga med att bevara ärendet.

### <a name="multiple-container-database-mdc-restore"></a>Återställning av MDC (Multiple container Database)

I flera container-databaser för HANA är standard konfigurationen SYSTEMDB + 1 eller flera klient-databaser. Att återställa en hel SAP HANA instans innebär att återställa både SYSTEMDB-och klient databaser. En återställer SYSTEMDB först och fortsätter sedan för klient organisations databasen. System DB innebär i princip att åsidosätta system informationen på det valda målet. Den här återställningen åsidosätter också BackInt-relaterad information i mål instansen. Så när system databasen har återställts till en mål instans kör du skriptet för för registrering igen. Det går bara att återställa efterföljande klient databas återställningar.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Uppgraderar från SAP HANA 1,0 till 2,0

Om du skyddar SAP HANA 1,0-databaser och vill uppgradera till 2,0 utför du följande steg:

- [Stoppa skyddet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med Behåll data för den gamla SDC-databasen.
- Genomför uppgraderingen. Efter slut för ande är HANA nu MDC med en system databas och klient organisations databas (er)
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana) med rätt information om (sid och MDC).
- Omregistrera tillägget för samma dator i Azure Portal (säkerhets kopierings > Visa information – > Välj den relevanta Azure VM-> omregistrera).
- Klicka på identifiera om databaser för samma virtuella dator. Den här åtgärden ska visa den nya databaser i steg 2 med rätt information (SYSTEMDB och klient organisations databasen, inte SDC).
- Konfigurera säkerhets kopiering för dessa nya databaser.

## <a name="upgrading-without-an-sid-change"></a>Uppgradera utan en SID-ändring

Uppgraderingar av OS eller SAP HANA som inte orsakar en SID ändring kan hanteras enligt beskrivningen nedan:

- [Stoppa skyddet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med Behåll data för databasen
- Genomför uppgraderingen.
- Kör [skriptet för för registrering](https://aka.ms/scriptforpermsonhana)igen. Vi har vanligt vis sett uppgraderings processen som tar bort de nödvändiga rollerna. Genom att köra skriptet för för registrering kan du kontrol lera alla nödvändiga roller.
- [Återuppta skyddet](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) av databasen igen

## <a name="re-registration-failures"></a>Försök att registrera igen

Kontrol lera om det finns ett eller flera av följande symptom innan du utlöser omregistrerings åtgärden:

- Alla åtgärder (till exempel säkerhets kopiering, återställning och konfigurations säkerhets kopiering) kan inte utföras på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Om **säkerhets kopierings status** fältet för det säkerhetskopierade objektet **visas kan du**utesluta alla andra orsaker som kan resultera i samma status:

  - Saknar behörighet att utföra säkerhetskopierade åtgärder på den virtuella datorn
  - Den virtuella datorn har stängts av, så säkerhets kopieringen kan inte ske
  - Nätverks problem

Dessa symtom kan uppstå på grund av en eller flera av följande orsaker:

- Ett tillägg har tagits bort eller avinstallerats från portalen.
- Den virtuella datorn återställdes i tid genom återställning av disk på plats.
- Den virtuella datorn stängdes under en längre period, så tilläggs konfigurationen på den har upphört att gälla.
- Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn och i samma resurs grupp som den borttagna virtuella datorn.

I föregående scenarier rekommenderar vi att du utlöser en ny registrering på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Läs [vanliga frågor](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) om hur du säkerhetskopierar SAP HANA databaser på virtuella Azure-datorer.
