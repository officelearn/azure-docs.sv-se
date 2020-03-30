---
title: Felsöka SAP HANA-databaser säkerhetskopieringsfel
description: Beskriver felsÃ¶kning av vanliga fel som kan uppstÃ¥ nÃ¤r du ansÃ¤nder Azure Backup för att säkerhetskopiera SAP HANA-databaser.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459318"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Felsöka säkerhetskopiering av SAP HANA-databaser på Azure

Den här artikeln innehåller felsökningsinformation för säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer. Mer information om sap hana-säkerhetskopieringsscenarierna som vi för närvarande stöder finns i [scenariostöd](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Förutsättningar och behörigheter

Se [förutsättningarna](tutorial-backup-sap-hana-db.md#prerequisites) och [vad förregistreringsskriptet gör](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) innan du konfigurerar säkerhetskopior.

## <a name="common-user-errors"></a>Vanliga användarfel

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Felmeddelande**      | <span style="font-weight:normal">Azure-säkerhetskopiering har inte behörighet att utföra säkerhetskopiering</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Rollen kan ha skrivits över.                          |
| **Rekommenderad åtgärd** | LÃ¶s problemet genom att kÃ¶sa skriptet **frÃ¥n fönstret Upptäck DB** eller hämta det [här](https://aka.ms/scriptforpermsonhana). Du kan också lägga till rollen "SAP_INTERNAL_HANA_SUPPORT" i AzureWLBACKUPHANAUSER (Workload Backup User). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInÖppnaHanaOdbcConnection

| Felmeddelande      | <span style="font-weight:normal">Det gick inte att ansluta till HANA-systemet</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen kan vara nere.<br/>De behörigheter som krävs för Azure-säkerhetskopiering för att interagera med HANA-databasen har inte angetts. |
| **Rekommenderad åtgärd** | Kontrollera om SAP HANA-databasen är uppe. Om databasen är igång kontrollerar du om alla nödvändiga behörigheter har angetts. Om någon av behörigheterna saknas kör [förregistreringsskriptet](https://aka.ms/scriptforpermsonhana) för att lägga till de saknade behörigheterna. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA-instansen är antingen ogiltig eller kan inte hittas</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Flera SAP HANA-instanser på en enda Azure-virtuell dator kan inte säkerhetskopieras. |
| **Rekommenderad åtgärd** | Kör [förregistreringsskriptet](https://aka.ms/scriptforpermsonhana) på DEN SAP HANA-instans som du vill säkerhetskopiera. Om problemet kvarstår kontaktar du Microsoft-supporten. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Felmeddelande      | <span style="font-weight:normal">Den angivna SAP HANA-åtgärden stöds inte</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure-säkerhetskopiering för SAP HANA stöder inte inkrementell säkerhetskopiering och åtgärder som utförs på SAP HANA-inbyggda klienter (Studio/ Cockpit/ DBA Cockpit) |
| **Rekommenderad åtgärd** | För mer information, se [här](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Felmeddelande      | <span style="font-weight:normal">Den här SAP HANA-databasen stöder inte den begärda säkerhetskopieringstypen</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Azure-säkerhetskopiering stöder inte inkrementell säkerhetskopiering och säkerhetskopiering med hjälp av ögonblicksbilder |
| **Rekommenderad åtgärd** | För mer information, se [här](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Felmeddelande      | <span style="font-weight:normal">Reservloggkedjan är bruten</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Loggsäkerhetsplatsen kan ha uppdaterats från backint till filsystem eller så kan den körbara backinten ha ändrats |
| **Rekommenderad åtgärd** | Utlösa en fullständig säkerhetskopiering för att lösa problemet                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Felmeddelande      | <span style="font-weight:normal">Käll- och målsystemen för återställning är oförenliga</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Målsystemet för återställning är inkompatibelt med källan |
| **Rekommenderad åtgärd** | Läs SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) om du vill veta mer om de återställningstyper som stöds idag |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCFörgraderad Nedtecterad

| Felmeddelande      | <span style="font-weight:normal">SDC till MDC uppgradering upptäckt</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | SAP HANA-instansen har uppgraderats från SDC till MDC. Säkerhetskopior misslyckas efter uppdateringen. |
| **Rekommenderad åtgärd** | Följ stegen i [avsnittet Uppgradera från SAP HANA 1.0 till 2.0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) för att lösa problemet |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Felmeddelande      | <span style="font-weight:normal">Ogiltig bakåtsträvningskonfiguration har upptäckts</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Möjliga orsaker**    | Stödparametrarna har angetts felaktigt för Azure-säkerhetskopiering |
| **Rekommenderad åtgärd** | Kontrollera om följande (bakåtsträvbara) parametrar är inställda:<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:falskt]<br/>\*[parallel_data_backup_backint_channels:1]<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>Om bakåtinte-baserade parametrar finns i HOST tar du bort dem. Om parametrar inte finns på VÄRD-nivå men har ändrats manuellt på databasnivå återställer du dem till lämpliga värden som beskrivits tidigare. Du kan också köra [stoppskydd och behålla säkerhetskopierade data](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) från Azure-portalen och välj sedan **Återuppta säkerhetskopiering**. |

## <a name="restore-checks"></a>Återställa kontroller

### <a name="single-container-database-sdc-restore"></a>SDC-återställning (Single Container Database)

Ta hand om indata samtidigt som du återställer en enda behållardatabas (SDC) för HANA till en annan SDC-maskin. Databasnamnet ska anges med gemener och med "sdc" bifogat inom parentes. HANA-instansen visas i versaler.

Anta att en SDC HANA-instans "H21" säkerhetskopieras. Sidan säkerhetskopieringsobjekt visar namnet på säkerhetskopian som **"h21(sdc)".** Om du försöker återställa databasen till ett annat mål SDC, säger H11, då följande ingångar måste tillhandahållas.

![Återställt SDC-databasnamn](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observera följande punkter:

- Som standard fylls det återställda db-namnet i med namnet på säkerhetskopieringsobjektet. I det här fallet h21(sdc).
- Om du väljer målet som H11 ändras INTE det återställda db-namnet automatiskt. **Den ska redigeras till h11(sdc).** När det gäller SDC kommer det återställda db-namnet att vara målinstans-ID med gemener och "sdc" bifogade inom parentes.
- Eftersom SDC bara kan ha en enda databas måste du också klicka på kryssrutan för att tillåta åsidosättning av befintliga databasdata med återställningspunktdata.
- Linux är skiftlägeskänsligt. Så var noga med att bevara fallet.

### <a name="multiple-container-database-mdc-restore"></a>Mdc-återställning (Multiple Container Database)

I flera behållardatabaser för HANA är standardkonfigurationen SYSTEMDB + 1 eller fler klientDB: er. Återställa en hel SAP HANA-instans innebär att återställa både SYSTEMDB och Tenant DBs. En återställer SYSTEMDB först och fortsätter sedan för Tenant DB. System DB innebär i huvudsak att åsidosätta systeminformationen på det valda målet. Den här återställningen åsidosätter också BackInt-relaterad information i målinstansen. Så när systemet DB återställs till en målinstans, kör förregistrering skriptet igen. Först då lyckas de efterföljande klient-DB-återställningarna.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Uppgradering från SAP HANA 1.0 till 2.0

Om du skyddar SAP HANA 1.0-databaser och vill uppgradera till 2.0 gör du följande:

- [Stoppa skyddet](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med lagringsdata för gamla SDC-databas.
- Utför uppgraderingen. Efter avslutad är HANA nu MDC med ett system DB och tenant DB(s)
- Kör [förregistrering skript](https://aka.ms/scriptforpermsonhana) med korrekta uppgifter om (sid och MDC).
- Registrera tillägg för samma dator i Azure-portalen (Säkerhetskopiering -> visa information -> Välj relevant Azure VM -> Omregistrera dig).
- Klicka på Återupptäck DBs för samma virtuella dator. Den här åtgärden bör visa de nya DBs i steg 2 med korrekta uppgifter (SYSTEMDB och Tenant DB, inte SDC).
- Konfigurera säkerhetskopiering för dessa nya databaser.

## <a name="upgrading-without-an-sid-change"></a>Uppgradera utan SID-ändring

Uppgraderingar till OS eller SAP HANA som inte orsakar en SID-ändring kan hanteras enligt beskrivningen nedan:

- [Stoppa skydd](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) med lagringsdata för databasen
- Utför uppgraderingen.
- Kör [skriptet för förhandsregistrering](https://aka.ms/scriptforpermsonhana)igen . Vanligtvis har vi sett uppgraderingsprocessen tar bort de nödvändiga rollerna. Om du kör skriptet för förhandsregistrering kan du verifiera alla nödvändiga roller.
- [Återuppta skyddet](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) för databasen igen

## <a name="re-registration-failures"></a>Fel vid omregistrering

Kontrollera om det finns ett eller flera av följande symptom innan du utlöser omregistreringen:

- Alla åtgärder (t.ex. säkerhetskopiering, återställning och konfigurera säkerhetskopiering) misslyckas på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstallerad, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Om området **Säkerhetskopieringsstatus** för säkerhetskopieringsobjektet inte kan **nås**utesluter du alla andra orsaker som kan resultera i samma status:

  - Brist på behörighet att utföra säkerhetskopieringsrelaterade åtgärder på den virtuella datorn
  - Den virtuella datorn är avstängd, så säkerhetskopior kan inte äga rum
  - Nätverksproblem

Dessa symtom kan uppstå av en eller flera av följande skäl:

- Ett tillägg har tagits bort eller avinstallerats från portalen.
- Den virtuella datorn återställdes tillbaka i tiden genom diskåterställning på plats.
- Den virtuella datorn stängdes av under en längre period, så förlängningskonfigurationen på den upphörde att gälla.
- Den virtuella datorn togs bort och en annan virtuell dator skapades med samma namn och i samma resursgrupp som den borttagna virtuella datorn.

I föregående scenarier rekommenderar vi att du utlöser en omregistrerad åtgärd på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Granska vanliga [frågor](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) om säkerhetskopiering av SAP HANA-databaser på virtuella Azure-datorer.
