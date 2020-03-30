---
title: Felsöka säkerhetskopiering av SQL Server-databasen
description: Felsöka information för säkerhetskopiering av SQL Server-databaser som körs på virtuella Azure-datorer med Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408624"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Felsöka säkerhetskopiering av SQL Server-databasen med hjälp av Azure Backup

Den här artikeln innehåller felsökningsinformation för SQL Server-databaser som körs på virtuella Azure-datorer.

Mer information om säkerhetskopieringsprocessen och begränsningar finns [i Om SQL Server-säkerhetskopiering i virtuella Azure-datorer](sql-support-matrix.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

Om du vill konfigurera skydd för en SQL Server-databas på en virtuell dator måste du installera **azurebackupwindowsWorkload-tillägget** på den virtuella datorn. Om du får felet **UserErrorSQLNoSysadminMembership**betyder det att din SQL Server-instans inte har de behörigheter för säkerhetskopiering som krävs. Åtgärda det här felet genom att följa stegen i [Ange vm-behörigheter](backup-azure-sql-database.md#set-vm-permissions).

## <a name="troubleshoot-discover-and-configure-issues"></a>Felsöka identifiera och konfigurera problem

När du har skapat och konfigurerat ett Recovery Services-valv är det en tvåstegsprocess att upptäcka databaser och konfigurera säkerhetskopiering.<br>

![sql](./media/backup-azure-sql-database/sql.png)

Under säkerhetskopieringskonfigurationen, om SQL VM och dess instanser inte är synliga i **identifierings-DB:erna i virtuella datorer** och **Konfigurera säkerhetskopiering** (se ovan avbildning) ska du se till att:

### <a name="step-1-discovery-dbs-in-vms"></a>Steg 1: Identifierings-DB:er i virtuella datorer

- Om den virtuella datorn inte finns med i listan upptäckte virtuella datorer och inte heller är registrerad för SQL-säkerhetskopiering i ett annat valv följer du stegen för säkerhetskopiering av [Identifierings-SQL Server.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)

### <a name="step-2-configure-backup"></a>Steg 2: Konfigurera säkerhetskopiering

- Om valvet där SQL VM är registrerat i samma valv som används för att skydda databaserna följer du stegen [Konfigurera säkerhetskopiering.](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)

Om DEN SQL VM nödvändigtvis till vara registrerat i det ny valv, då den måste vara unregistered från gammal valv.  Om du vill ta bort en VIRTUELL SQL-dator från valvet måste alla skyddade datakällor stoppas och sedan kan du ta bort säkerhetskopierade data. Att ta bort säkerhetskopierade data är en destruktiv åtgärd.  När du har granskat och vidtagit alla försiktighetsåtgärder för att avregistrera SQL-virtuella datorn registrerar du sedan samma virtuella dator med ett nytt valv och försöker igen säkerhetskopieringen.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Felsöka problem med säkerhetskopiering och återställning  

Ibland kan antingen slumpmässiga fel inträffa i säkerhetskopierings- och återställningsåtgärder eller så kan dessa åtgärder fastna. Detta kan bero på antivirusprogram på den virtuella datorn. Vi föreslår följande steg på bästa sätt:

1. Uteslut följande mappar från antivirusskanning:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    Ersätt `C:\` med bokstaven i *SystemDrive*.

1. Uteslut följande tre processer som körs inom en virtuell dator från antivirusskanning:

    - IaasWLPluginSvc.exe
    - IaasWorkloadCoordinaorService.exe
    - TriggerExtensionJob.exe

1. SQL erbjuder också några riktlinjer om att arbeta med antivirusprogram. Mer information finns i [den här artikeln.](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server)

## <a name="error-messages"></a>Felmeddelanden

### <a name="backup-type-unsupported"></a>Säkerhetskopieringstyp stöds inte

| Severity | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | Aktuella inställningar för den här databasen stöder inte vissa typer av säkerhetskopieringar som finns i den associerade principen. | <li>Endast en fullständig säkerhetskopiering av databasen kan utföras på huvuddatabasen. Varken differentiell säkerhetskopiering eller säkerhetskopiering av transaktionsloggar är möjlig. </li> <li>Alla databaser i den enkla återställningsmodellen tillåter inte säkerhetskopiering av transaktionsloggar.</li> | Ändra databasinställningarna så att alla säkerhetskopieringstyper i principen stöds. Du kan också ändra den aktuella principen så att den bara innehåller de säkerhetskopieringstyper som stöds. Annars hoppas de som inte stöds över när säkerhetskopieringen är det, annars misslyckas säkerhetskopieringsjobbet på begäran.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL-databasen stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databasens återställningsmodell inte tillåter den begärda säkerhetskopieringstypen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas som använder en enkel återställningsmodell tillåter inte säkerhetskopiering av loggar.</li><li>Differentiella säkerhetskopior och loggsäkerhetskopior är inte tillåtna för en huvuddatabas.</li></ul>Mer information finns i dokumentationen för [SQL Server-återställningsmodeller.](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) | Om loggsäkerhetskopieringen misslyckas för databasen i den enkla återställningsmodellen provar du något av följande alternativ:<ul><li>Om databasen är i enkelt återställningsläge inaktiverar du säkerhetskopiering av loggen.</li><li>Använd [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) för att ändra databasåterställningsmodellen till fullständig eller massloggad. </li><li> Om du inte vill ändra återställningsmodellen och har en standardprincip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Dina fullständiga och differentiella säkerhetskopior fungerar per schema. Loggsäkerhetskopiorna hoppas över, vilket förväntas i det här fallet.</li></ul>Om det är en huvuddatabas och du har konfigurerat differentiell säkerhetskopiering eller loggsäkerhet använder du något av följande steg:<ul><li>Använd portalen för att ändra schemat för säkerhetskopieringsprincipen för huvuddatabasen till fullo.</li><li>Om du har en standardprincip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Din fullständiga säkerhetskopiering kommer att fungera per schema. Differentiella säkerhetskopior eller loggsäkerhetskopior kommer inte att hända, vilket förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en konfliktåtgärd redan kördes på samma databas. | Se [blogginlägget om begränsningar för säkerhetskopiering och återställning](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) som körs samtidigt.| [Använd SQL Server Management Studio (SSMS) för att övervaka säkerhetskopieringsjobben](manage-monitor-sql-database-backup.md). Starta om åtgärden efter att åtgärden står i konflikt.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen finns inte. | Databasen har antingen tagits bort eller bytt namn. | Kontrollera om databasen har tagits bort eller bytt namn på databasen.<br/><br/> Om databasen togs bort av misstag återställer du databasen till den ursprungliga platsen om du vill fortsätta med säkerhetskopior.<br/><br/> Om du har tagit bort databasen och inte behöver framtida säkerhetskopior väljer du Stoppa säkerhetskopiering med **Behåll säkerhetskopieringsdata** eller Ta bort **säkerhetskopierade data**i valvet för Återställningstjänster . **Retain Backup Data** Mer information finns i [Hantera och övervaka säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhetskopieras via en annan säkerhetskopieringslösning som trunkerar loggkedjan.|<ul><li>Kontrollera om en annan säkerhetskopieringslösning eller ett annat skript används. Stoppa i så fall den andra säkerhetskopieringslösningen. </li><li>Om säkerhetskopieringen var en loggsäkerhetskopia på begäran utlöser du en fullständig säkerhetskopia för att starta en ny loggkedja. För schemalagda loggsäkerhetskopior behövs ingen åtgärd eftersom Azure Backup-tjänsten automatiskt utlöser en fullständig säkerhetskopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup kan inte ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL Server-instansen. | Använd ytterligare information på felmenyn i Azure Portal för att begränsa grundorsakerna. Se [felsökning av SQL-säkerhetskopiering](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) för att åtgärda felet.<br/><ul><li>Om standardinställningarna för SQL inte tillåter fjärranslutningar ändrar du inställningarna. Mer information om hur du ändrar inställningarna finns i följande artiklar:<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns inloggningsproblem använder du dessa länkar för att åtgärda dem:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Första fullständiga säkerhetskopieringen saknas för den här datakällan. | Fullständig säkerhetskopiering saknas för databasen. Log och differentiella säkerhetskopior är föräldrar till en fullständig säkerhetskopia, så se till att ta fullständiga säkerhetskopior innan du utlöser differentiella eller logga säkerhetskopior. | Utlösa en fullständig säkerhetskopiering på begäran.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att ta backup eftersom transaktionsloggen för datakällan är full. | Databasens transaktionsloggutrymme är fullt. | Åtgärda problemet genom att läsa [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Databas med samma namn finns redan på målplatsen | Målet för målåterställning har redan en databas med samma namn.  | <ul><li>Ändra måldatabasnamnet.</li><li>Du kan också använda alternativet överför kraft på återställningssidan.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du gör en återställning måste måldatabasen kopplas från. Azure Backup kan inte koppla från dessa data. | Använd ytterligare information på felmenyn i Azure Portal för att begränsa grundorsakerna. Mer information finns i [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta servercertifikatet med tumavtryck på målet. | Huvuddatabasen för målinstansen har inget giltigt krypteringstumavtryck. | Importera det giltiga certifikattumavtrycket som används på källinstansen till målinstansen. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggsäkerhetskopian för återställning innehåller massloggade ändringar. De kan inte användas för att stoppa vid en godtycklig tidpunkt enligt SQL-riktlinjerna. | När en databas är i massloggat återställningsläge kan data mellan en massloggad transaktion och nästa loggtransaktion inte återställas. | Välj en annan tidpunkt för återställning. [Läs mer](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL AlwaysOn-tillgänglighetsgruppen uppfylls inte eftersom vissa noder i tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhetskopior registreras inte eller kan inte nås. | <ul><li>Kontrollera att alla noder som krävs för att utföra säkerhetskopior av den här databasen är registrerade och felfria och försöker sedan igen.</li><li>Ändra inställningen för säkerhetskopiering för sql server-gruppen Always On availability.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL server VM är antingen avstängning och inte tillgänglig för Azure Backup-tjänsten. | Den virtuella datorn stängs av. | Kontrollera att SQL Server-instansen körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup-tjänsten använder Azure VM-gästagent för säkerhetskopiering men gästagenten är inte tillgänglig på målservern. | Gästagenten är inte aktiverad eller är felfritt. | [Installera vm-gästagenten](../virtual-machines/extensions/agent-windows.md) manuellt. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Auto-protection Intent har antingen tagits bort eller är inte längre giltig. | När du aktiverar automatiskt skydd på en SQL Server-instans körs **Konfigurera säkerhetskopieringsjobb** för alla databaser i den instansen. Om du inaktiverar automatiskt skydd medan jobben körs avbryts **in-progress-jobben** med den här felkoden. | Aktivera automatiskt skydd igen för att skydda alla återstående databaser. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Åtgärden blockeras eftersom du har nått gränsen för antalet tillåtna åtgärder inom 24 timmar. | När du har nått den högsta tillåtna gränsen för en operation inom 24 timmar kommer det här felet. <br> Till exempel: Om du har nått gränsen för antalet konfigurera säkerhetskopieringsjobb som kan utlösas per dag, och du försöker konfigurera säkerhetskopiering på ett nytt objekt, visas det här felet. | Vanligtvis löser problemet problemet att försöka igen efter 24 timmar. Om problemet kvarstår kan du kontakta Microsoft-supporten för att få hjälp.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Åtgärden blockeras eftersom valvet har nått sin maximala gräns för sådana operationer som tillåts inom 24 timmar. | När du har nått den högsta tillåtna gränsen för en operation inom 24 timmar kommer det här felet. Det här felet kommer vanligtvis när det finns storskaliga åtgärder som att ändra principen eller automatiskt skydd. Till skillnad från i fallet med CloudDosAbsoluteLimitReached finns det inte mycket du kan göra för att lösa det här tillståndet, i själva verket kommer Azure Backup-tjänsten att försöka igen åtgärderna internt för alla objekt i fråga.<br> Till exempel: Om du har ett stort antal datakällor skyddade med en princip och du försöker ändra den principen, utlöser den konfigurera skyddsjobb för var och en av de skyddade objekten och ibland kan det nå den högsta tillåtna gränsen för sådana åtgärder per dag.| Azure Backup-tjänsten kommer automatiskt att försöka den här åtgärden efter 24 timmar.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Den virtuella datorn kan inte kontakta Azure Backup-tjänsten på grund av problem med internetanslutning. | Den virtuella datorn behöver utgående anslutning till Azure Backup Service, Azure Storage eller Azure Active Directory-tjänster.| - Om du använder NSG för att begränsa anslutningen bör du använda AzureBackup-tjänsttaggen för att ge utgående åtkomst till Azure Backup till Azure Backup Service, Azure Storage eller Azure Active Directory-tjänster. Följ dessa [steg](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags) för att bevilja åtkomst.<br>- Se till att DNS löser Azure-slutpunkter.<br>- Kontrollera om den virtuella datorn ligger bakom en belastningsutjämnare som blockerar internetåtkomst. Genom att tilldela offentliga IP till de virtuella datorerna fungerar identifieringen.<br>- Kontrollera att det inte finns någon brandvägg / antivirus / proxy som blockerar samtal till ovanstående tre måltjänster.

## <a name="re-registration-failures"></a>Fel vid omregistrering

Kontrollera om det finns ett eller flera av följande symptom innan du utlöser omregistreringen:

* Alla åtgärder (t.ex. säkerhetskopiering, återställning och konfigurera säkerhetskopiering) misslyckas på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstallerad**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Om området **Säkerhetskopieringsstatus** för säkerhetskopieringsobjektet inte kan **nås**utesluter du alla andra orsaker som kan resultera i samma status:

  * Brist på behörighet att utföra säkerhetskopieringsrelaterade åtgärder på den virtuella datorn.
  * Avstängning av den virtuella datorn, så säkerhetskopiering kan inte ske.
  * Nätverksproblem.

   ![registrera om den virtuella datorn](./media/backup-azure-sql-database/re-register-vm.png)



* När det gäller en grupp för alltid på tillgänglighet började säkerhetskopiorna misslyckas efter att du ändrat inställningen för säkerhetskopiering eller efter en redundans.

Dessa symtom kan uppstå av en eller flera av följande skäl:

* Ett tillägg har tagits bort eller avinstallerats från portalen.
* Ett tillägg avinstallerades från **Kontrollpanelen** på den virtuella datorn under **Avinstallera eller Ändra ett program**.
* Den virtuella datorn återställdes tillbaka i tiden genom diskåterställning på plats.
* Den virtuella datorn stängdes av under en längre period, så förlängningskonfigurationen på den upphörde att gälla.
* Den virtuella datorn togs bort och en annan virtuell dator skapades med samma namn och i samma resursgrupp som den borttagna virtuella datorn.
* En av tillgänglighetsgruppnoderna fick inte den fullständiga säkerhetskopieringskonfigurationen. Detta kan inträffa när tillgänglighetsgruppen registreras i valvet eller när en ny nod läggs till.

I föregående scenarier rekommenderar vi att du utlöser en omregistrerad åtgärd på den virtuella datorn. Se [här](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) för instruktioner om hur du utför den här uppgiften i PowerShell.

## <a name="size-limit-for-files"></a>Storleksgräns för filer

Den totala strängstorleken för filer beror inte bara på antalet filer utan också på deras namn och sökvägar. Hämta det logiska filnamnet och den fysiska sökvägen för varje databasfil. Du kan använda den här SQL-frågan:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Nu ordna dem i följande format:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Här är ett exempel:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Om strängstorleken för innehållet överstiger 20 000 byte lagras databasfilerna på olika sätt. Under återställningen kan du inte ange sökvägen till målfilen för återställning. Filerna återställs till den sql-standardsökväg som tillhandahålls av SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Åsidosätt filsökvägen för standardmålåterställning

Du kan åsidosätta sökvägen till målåterställningsfilen under återställningen genom att placera en JSON-fil som innehåller mappningen av databasfilen till sökvägen för målåterställning. Skapa `database_name.json` en fil och placera den på platsen *C:\Program\Azure Workload Backup\bin\plugins\SQL*.

Innehållet i filen bör vara i detta format:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Här är ett exempel:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

I föregående innehåll kan du hämta det logiska namnet på databasfilen med hjälp av följande SQL-fråga:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

Den här filen bör placeras innan du utlöser återställningen.

## <a name="next-steps"></a>Nästa steg

Mer information om Virtuella Azure Backup för virtuella SQL Server-datorer (offentlig förhandsversion) finns i [Azure Backup för virtuella SQL-datorer](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
