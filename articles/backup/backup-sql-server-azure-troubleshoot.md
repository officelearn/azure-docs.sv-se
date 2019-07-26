---
title: Felsöka SQL Server säkerhets kopiering av databasen med Azure Backup | Microsoft Docs
description: Felsöknings information för att säkerhetskopiera SQL Server databaser som körs på virtuella Azure-datorer med Azure Backup.
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: c601ecb6997834aa216de094e2809670833dd9cb
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464922"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Felsöka SQL Server säkerhets kopiering av databasen med Azure Backup

Den här artikeln innehåller felsöknings information för SQL Server databaser som körs på virtuella Azure-datorer.

Mer information om säkerhets kopierings processen och begränsningar finns i [om SQL Server säkerhets kopiering på virtuella Azure-datorer](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

Om du vill konfigurera skydd för en SQL Server-databas på en virtuell dator måste du installera **AzureBackupWindowsWorkload** -tillägget på den virtuella datorn. Om du får felet **UserErrorSQLNoSysadminMembership**innebär det att din SQL Server-instans inte har de säkerhets kopierings behörigheter som krävs. Följ stegen i [Ange VM-behörigheter](backup-azure-sql-database.md#set-vm-permissions)för att åtgärda felet.

## <a name="error-messages"></a>Felmeddelanden

### <a name="backup-type-unsupported"></a>Säkerhets kopierings typen stöds inte

| severity | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | De aktuella inställningarna för den här databasen stöder inte vissa säkerhets kopierings typer som finns i den tillhör ande principen. | <li>Endast en fullständig databas säkerhets kopierings åtgärd kan utföras på huvud databasen. Varken differentiell säkerhets kopiering eller säkerhets kopiering av transaktions logg är möjlig. </li> <li>Alla databaser i den enkla återställnings modellen tillåter inte säkerhets kopiering av transaktions loggar.</li> | Ändra databas inställningarna så att alla säkerhets kopierings typer i principen stöds. Du kan också ändra den aktuella principen så att den bara innehåller de säkerhets kopierings typer som stöds. Annars hoppas de säkerhets kopierings typer som inte stöds över under schemalagd säkerhets kopiering, eller så Miss söker säkerhets kopierings jobbet för ad hoc-säkerhetskopiering.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL-databasen stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databas återställnings modellen inte tillåter den begärda säkerhets kopierings typen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas som använder en enkel återställnings modell tillåter inte logg säkerhets kopiering.</li><li>Det går inte att säkerhetskopiera differentiella och loggar för en huvud databas.</li></ul>Mer information finns i dokumentationen för [SQL Server återställnings modeller](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Om logg säkerhets kopieringen Miss lyckas för databasen i den enkla återställnings modellen kan du prova något av följande alternativ:<ul><li>Om databasen är i enkelt återställnings läge inaktiverar du logg säkerhets kopior.</li><li>Använd [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) om du vill ändra databas återställnings modellen till fullständig eller Mass utloggning. </li><li> Om du inte vill ändra återställnings modellen och du har en standard princip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Dina fullständiga och differentiella säkerhets kopieringar fungerar per schema. Logg säkerhets kopiorna hoppas över, vilket förväntas i det här fallet.</li></ul>Om det är en huvud databas och du har konfigurerat differentiell eller logg säkerhets kopiering, använder du något av följande steg:<ul><li>Använd portalen för att ändra schemat för säkerhets kopierings policyn för Master-databasen till full.</li><li>Om du har en standard princip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Den fullständiga säkerhets kopieringen fungerar per schema. Differentiella eller logga säkerhets kopieringar sker inte, vilket förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Se [blogg inlägget om begränsningar för säkerhets kopiering och återställning](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) som körs samtidigt.| [Använd SQL Server Management Studio (SSMS) för att övervaka säkerhets kopierings jobben](manage-monitor-sql-database-backup.md). När den motstridiga åtgärden Miss lyckas startar du om åtgärden.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen finns inte. | Databasen har antingen tagits bort eller bytt namn. | Kontrol lera om databasen har tagits bort av misstag eller fått ett nytt namn.<br/><br/> Om databasen har tagits bort av misstag, om du vill fortsätta med säkerhets kopieringarna återställer du databasen till den ursprungliga platsen.<br/><br/> Om du har tagit bort databasen och inte behöver framtida säkerhets kopieringar väljer du **stoppa säkerhets kopiering** med **Behåll säkerhets kopierings data** eller **ta bort säkerhetskopierade data**i Recovery Services valvet. Mer information finns i [Hantera och övervaka säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhets kopie ras via en annan säkerhets kopierings lösning som trunkerar logg kedjan.|<ul><li>Kontrol lera om någon annan lösning för säkerhets kopiering eller skript används. Om så är fallet, stoppa den andra säkerhets kopierings lösningen. </li><li>Om säkerhets kopieringen var ad hoc-säkerhetskopiering, Utlös en fullständig säkerhets kopiering för att starta en ny logg kedja. För schemalagda säkerhets kopieringar behövs ingen åtgärd eftersom Azure Backups tjänsten automatiskt aktiverar en fullständig säkerhets kopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup kan inte ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL Server-instansen. | Använd den ytterligare informationen på menyn Azure Portal fel för att begränsa rotor saken. Åtgärda problemet genom att läsa fel [sökning av SQL-säkerhetskopiering](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) .<br/><ul><li>Om standard-SQL-inställningarna inte tillåter fjärr anslutningar ändrar du inställningarna. I följande artiklar finns information om hur du ändrar inställningarna:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns inloggnings problem kan du använda dessa länkar för att åtgärda dem:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den första fullständiga säkerhets kopieringen saknas för den här data källan. | Fullständig säkerhets kopiering saknas för databasen. Logg-och differentiella säkerhets kopieringar är överordnade till en fullständig säkerhets kopia, så se till att ta fullständiga säkerhets kopior innan du utlöser differentiella eller loggar säkerhets kopior. | Utlös en fullständig ad hoc-säkerhetskopiering.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att säkerhetskopiera eftersom transaktions loggen för data källan är full. | Databasens transaktions logg utrymme är full. | Information om hur du löser det här problemet finns i [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det finns redan en databas med samma namn på mål platsen | Mål återställnings målet har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål databasen.</li><li>Eller Använd alternativet framtvinga överskrivning på sidan Återställ.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du utför en återställning måste mål databasen försättas i offlineläge. Azure Backup kan inte ta dessa data offline. | Använd den ytterligare informationen på menyn Azure Portal fel för att begränsa rotor saken. Mer information finns i SQL Server- [dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta Server certifikatet med tumavtryck på målet. | Huvud databasen på mål instansen har inte något giltigt krypterings-tumavtryck. | Importera det giltiga tumavtrycket för certifikatet som används på käll instansen till mål instansen. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggsäkerhetskopian för återställning innehåller massloggade ändringar. De kan inte användas för att stoppa vid en godtycklig tidpunkt enligt SQL-riktlinjerna. | När en databas är i bulk-loggat återställnings läge går det inte att återställa data mellan en bulk-loggad transaktion och nästa logg transaktion. | Välj en annan tidpunkt för återställning. [Läs mer](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL AlwaysOn-tillgänglighetsgruppen uppfylls inte eftersom vissa noder i tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhets kopieringar är inte registrerade eller går inte att komma åt. | <ul><li>Se till att alla noder som krävs för att utföra säkerhets kopiering av databasen är registrerade och felfria och försök sedan igen.</li><li>Ändra inställningarna för säkerhets kopiering för SQL Server Always on-tillgänglighetsgrupper.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den virtuella SQL Server-datorn stängs av och är inte tillgänglig för Azure Backup-tjänsten. | Den virtuella datorn stängs av. | Se till att SQL Server-instansen körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup tjänsten använder Azures gästa Gent för virtuella datorer för säkerhets kopiering men gäst agenten är inte tillgänglig på mål servern. | Gäst agenten är inte aktive rad eller är skadad. | [Installera gäst agenten för den virtuella datorn](../virtual-machines/extensions/agent-windows.md) manuellt. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Automatiskt skydds avsikt har antingen tagits bort eller är inte mer giltigt. | När du aktiverar automatiskt skydd på en SQL Server instans måste du **Konfigurera säkerhets kopierings** jobb för alla databaser i den instansen. Om du inaktiverar automatiskt skydd medan jobben körs, avbryts **pågående** jobb med den här felkoden. | Aktivera automatiskt skydd en gång om du vill skydda alla återstående databaser. |

## <a name="re-registration-failures"></a>Försök att registrera igen

Kontrol lera om det finns ett eller flera av följande symptom innan du utlöser omregistrerings åtgärden:

* Alla åtgärder (till exempel säkerhets kopiering, återställning och konfigurations säkerhets kopiering) kan inte utföras på den virtuella datorn med någon av följande fel koder: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Det går **inte att komma åt**säkerhets kopierings **status** fältet för objektet som ska visas. Ta bort en regel för alla andra orsaker som kan resultera i samma status:

  * Saknar behörighet att utföra säkerhetskopierade åtgärder på den virtuella datorn  
  * Stänga av den virtuella datorn så att säkerhets kopieringen inte kan genomföras
  * Nätverksproblem  

  ![Status "kan inte uppnås" vid omregistrering av en virtuell dator](./media/backup-azure-sql-database/re-register-vm.png)

* Om det finns en Always on-tillgänglighets grupp startade säkerhets kopieringen inte när du ändrade inställningarna för säkerhets kopieringen eller efter en redundansväxling.

Dessa symtom kan uppstå på grund av en eller flera av följande orsaker:

* Ett tillägg har tagits bort eller avinstallerats från portalen. 
* Ett tillägg avinstallerades från **kontroll panelen** på den virtuella datorn under **Avinstallera eller ändra ett program**.
* Den virtuella datorn återställdes i tid genom återställning av disk på plats.
* Den virtuella datorn stängdes under en längre period, så tilläggs konfigurationen på den har upphört att gälla.
* Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn och i samma resurs grupp som den borttagna virtuella datorn.
* En av noderna i tillgänglighets gruppen fick inte den fullständiga konfigurationen för säkerhets kopiering. Detta kan inträffa när tillgänglighets gruppen är registrerad på valvet eller när en ny nod läggs till.

I föregående scenarier rekommenderar vi att du utlöser en ny registrering på den virtuella datorn. För närvarande är det här alternativet endast tillgängligt via PowerShell.

## <a name="size-limit-for-files"></a>Storleks gräns för filer

Den totala sträng storleken för filer beror inte bara på antalet filer, utan även på deras namn och sökvägar. Hämta det logiska fil namnet och den fysiska sökvägen för varje databas fil. Du kan använda SQL-fr åga:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Ändra nu i följande format:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Här är ett exempel:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Om innehålls sträng storleken överskrider 20 000 byte, lagras databasfilerna på olika sätt. Under återställningen kan du inte ange sökvägen till mål filen för återställning. Filerna kommer att återställas till standard-SQL-sökvägen som tillhandahålls av SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Åsidosätt fil Sök vägen för standard målets återställning

Du kan åsidosätta sökvägen till mål återställnings filen under återställnings åtgärden genom att placera en JSON-fil som innehåller mappningen av databas filen till mål återställnings Sök vägen. Skapa en `database_name.json` fil och placera den på platsen *C:\Program Files\Azure arbets belastning Backup\bin\plugins\SQL*.

Filens innehåll ska ha följande format:
```
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

```
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

I föregående innehåll kan du hämta databas filens logiska namn genom att använda följande SQL-fr åga:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Den här filen bör placeras innan du utlöser återställnings åtgärden.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup för SQL Server virtuella datorer (offentlig för hands version) finns i [Azure Backup för virtuella SQL-datorer](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
