---
title: Felsöka säkerhetskopiering av SQL Server-databas med hjälp av Azure Backup | Microsoft Docs
description: Felsökningsinformation för att säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer med Azure Backup.
services: backup
author: anuragm
manager: sivan
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: b2822a3c7dfa23065f2cbd35ef4e506efae026f2
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704853"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Felsöka säkerhetskopiering av SQL Server-databas med hjälp av Azure Backup

Den här artikeln innehåller felsökningsinformation för SQL Server-databaser som körs på virtuella Azure-datorer.

Mer information om processen för att säkerhetskopiera och begränsningar finns i [om SQL Server-säkerhetskopiering i Azure virtuella datorer](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

För att konfigurera skydd för en SQL Server-databas på en virtuell dator, måste du installera den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn. Om du får felet **UserErrorSQLNoSysadminMembership**, betyder det SQL Server-instansen inte har behörighet för säkerhetskopiering. Om du vill åtgärda det här felet följer du stegen i [Virtuella behörigheter](backup-azure-sql-database.md#set-vm-permissions).

## <a name="error-messages"></a>Felmeddelanden

### <a name="backup-type-unsupported"></a>Typ av säkerhetskopiering stöds inte

| Severity | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | Aktuella inställningar för den här databasen stöder inte vissa typer av säkerhetskopior finns i den associerade principen. | <li>Endast en fullständig säkerhetskopiering databasåtgärd kan utföras i master-databasen. Det går varken differentiell säkerhetskopiering eller säkerhetskopia av transaktionsloggen. </li> <li>Det tillåter inte någon databas i den enkla återställningsmodellen för säkerhetskopiering av transaktionsloggar.</li> | Ändra databasinställningarna så att alla säkerhetskopieringstyper i principen stöds. Eller ändra den aktuella principen om du vill inkludera endast säkerhetskopiering typer som stöds. I annat fall stöds inte säkerhetskopieringstyper kommer att åsidosättas vid schemalagd säkerhetskopiering eller säkerhetskopieringen misslyckas för ad hoc-säkerhetskopiering.


### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL database stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databasens Återställningsmodell inte tillåter den begärda säkerhetskopieringstypen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas som använder en enkel återställningsprocess tillåter inte loggsäkerhetskopiering.</li><li>Differentiella och loggbaserade säkerhetskopieringar tillåts inte för master-databasen.</li></ul>Mer information finns i den [återställningsmodeller för SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentation. | Om loggsäkerhetskopieringen misslyckas av databasen i den enkla återställningsmodellen, prova något av följande alternativ:<ul><li>Om databasen är i enkelt återställningsläge, inaktivera loggsäkerhetskopior.</li><li>Använd den [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) ändra databasens Återställningsmodell till fullständiga eller massloggade. </li><li> Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras om du inte vill ändra återställningsmodellen och ignorera felet. Din fullständiga och differentiella säkerhetskopieringar fungerar per schema. Säkerhetskopior av kommer att hoppas över, som förväntas i det här fallet.</li></ul>Om det är en master-databasen och du har konfigurerat differentiell eller säkerhetskopiering, använder du något av följande steg:<ul><li>Använda portalen för att ändra säkerhetskopieringsprincipen schemat för master-databasen till full.</li><li>Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras kan du ignorera felet. Fullständiga säkerhetskopieringen fungerar per schema. Differentiell eller log säkerhetskopieringar göras inte som förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Se den [blogginlägg om begränsningar för säkerhetskopiering och återställning](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) som körs samtidigt.| [Använda SQL Server Management Studio (SSMS) för att övervaka säkerhetskopieringsjobb](manage-monitor-sql-database-backup.md). Starta om åtgärden när den hindrande åtgärden misslyckas.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen finns inte. | Databasen har antingen tagits bort eller bytt namn. | Kontrollera om databasen av misstag har tagits bort eller bytt namn.<br/><br/> Om databasen tagits bort av misstag, om du vill fortsätta säkerhetskopiering, återställa databasen till den ursprungliga platsen.<br/><br/> Om du tog bort den och inte behöver framtida säkerhetskopior i Recovery Services-valvet markerar **stoppa säkerhetskopiering** med **Behåll säkerhetskopieringsdata** eller **ta bort säkerhetskopieringsdata**. Mer information finns i [hantera och övervaka säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhetskopieras via en annan säkerhetskopieringslösning som trunkerar loggkedjan.|<ul><li>Kontrollera om en annan lösning för säkerhetskopiering eller skript används. I så fall kan du stoppa den andra lösningen för säkerhetskopiering. </li><li>Om säkerhetskopieringen har en ad hoc-loggsäkerhetskopiering, utlösa en fullständig säkerhetskopiering för att starta en ny loggkedjan. Ingen åtgärd krävs för schemalagda säkerhetskopieringar, eftersom Azure Backup-tjänsten kommer automatiskt att aktivera en fullständig säkerhetskopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup är inte kan ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL Server-instansen. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Referera till [SQL felsöka säkerhetskopiering av](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) åtgärda felet.<br/><ul><li>Om standardinställningarna för SQL inte tillåta fjärranslutningar, ändra inställningarna. Se följande artiklar för information om hur du ändrar inställningarna:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns problem med inloggningen, kan du använda dessa länkar för att åtgärda dem:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Första fullständig säkerhetskopiering saknas för den här datakällan. | Fullständig säkerhetskopiering saknas för databasen. Logg- och differentiella säkerhetskopieringar är föräldrar att en fullständig säkerhetskopia, så var noga med att ta fullständiga säkerhetskopior innan du aktiverar RDC eller säkerhetskopior. | Utlös en ad hoc-fullständig säkerhetskopiering.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att säkerhetskopiera eftersom transaktionsloggen för datakällan är full. | Transaktionell loggutrymmet för databasen är full. | Om du vill åtgärda problemet genom att referera till den [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Databasen med samma namn finns redan på målplatsen | Återställningsmål mål har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål-databasen.</li><li>Du kan också använda alternativet force överskrivning på sidan för återställning.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du gör en återställning, måste måldatabasen tas offline. Azure Backup kan inte ta med dessa data offline. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Mer information finns i den [SQL Server-dokumentationen](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta servercertifikat med tumavtryck på målet. | Master-databasen på mål-instansen har inte ett giltigt tumavtryck. | Importera giltigt certifikat-tumavtrycket används på målinstansen på målinstansen. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggsäkerhetskopian för återställning innehåller massloggade ändringar. De kan inte användas för att stoppa vid en godtycklig tidpunkt enligt SQL-riktlinjerna. | När en databas är i återställningsläge massloggade kan kan data mellan en massloggade transaktion och nästa log transaktionen inte återställas. | Välj en annan tidpunkt för återställning. [Läs mer](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).


### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL AlwaysOn-tillgänglighetsgruppen uppfylls inte eftersom vissa noder i tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhetskopieringar har registrerats inte eller inte kan nås. | <ul><li>Kontrollera att alla noder som krävs för att utföra säkerhetskopieringar av den här databasen är registrerade och felfria och försök sedan igen.</li><li>Ändra säkerhetskopieringsinställningen för SQL Server Always On availability-gruppen.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQLServer-dator är antingen avstängd eller inte tillgänglig för Azure Backup-tjänsten. | Virtuellt datorn stängs av. | Se till att SQL Server-instansen körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup-tjänsten använder Virtuella Azure-gästagenten för säkerhetskopiering men gästagenten är inte tillgängliga på målservern. | Gästagenten är inte aktiverat eller är skadad. | [Installera virtuella datorns gästagent](../virtual-machines/extensions/agent-windows.md) manuellt. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Automatiskt skydd avsett har antingen tagits bort eller är inte längre giltig. | När du aktiverar automatiskt skydd på en SQL Server-instans **Konfigurera säkerhetskopiering** uppgifter har körts för alla databaser i instansen. Om du inaktiverar automatiskt skydd medan jobben körs, kommer **pågår** jobb avbryts med den här felkoden. | Aktivera automatiskt skydd igen för att skydda alla återstående databaser. |

## <a name="re-registration-failures"></a>Omregistrering fel

Sök efter en eller flera av följande innan du utlöser åtgärden registrera:

* Alla åtgärder (till exempel säkerhetskopiering, återställa och konfigurera säkerhetskopiering) misslyckas på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
* Den **Säkerhetskopieringsstatus** området för säkerhetskopieringsobjektet som visar **kan inte nås**. Utesluta alla andra orsaker som kan leda till samma status:

  * Bristande behörighet att utföra säkerhetskopiering-relaterade åtgärder på den virtuella datorn  
  * Stänga av den virtuella datorn, så att säkerhetskopieringar inte kan äga rum
  * Nätverksproblem  

  ![”Kan inte nås” status i Omregistrera på en virtuell dator](./media/backup-azure-sql-database/re-register-vm.png)

* När det gäller en Always On-tillgänglighetsgrupp igång säkerhetskopieringarna misslyckas när du har ändrat inställning för säkerhetskopiering eller efter en redundansväxling.

Dessa problem kan uppstå för en eller flera av följande orsaker:

* Tillägget har tagits bort eller avinstallerats från portalen. 
* Ett tillägg avinstallerades från **Kontrollpanelen** på den virtuella datorn under **avinstallera eller ändra ett Program**.
* Den virtuella datorn återställdes bakåt i tiden via diskåterställning på plats.
* Den virtuella datorn stängdes av under en längre period, så att tilläggskonfigurationen av på den upphört att gälla.
* Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn, i samma resursgrupp som den borttagna virtuella datorn.
* En av noderna tillgänglighet gruppen fick inte konfigurationen för fullständig säkerhetskopiering. Detta kan inträffa när tillgänglighetsgruppen har registrerats för valvet eller när du lägger till en ny nod.

I de föregående scenarierna rekommenderar vi att du utlöser en åtgärd som registrerar sig igen på den virtuella datorn. Det här alternativet är bara tillgänglig via PowerShell för tillfället.

## <a name="size-limit-for-files"></a>Storleksgränsen för filer

Den totala strängstorleken på filerna beror inte bara på hur många filer utan även på deras namn och sökvägar. Hämta logiskt filnamn och fysiska sökvägen för varje databasfil. Du kan använda den här SQL-frågan:

```
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Nu ordna dem i följande format:

```
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Här är ett exempel:

```
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Om strängstorleken på innehållet överstiger 20 000 byte, lagras databasfilerna på olika sätt. Under återställningen måste du inte ange sökvägen till målfilen för återställning. Filerna kommer att återställas till standardsökvägen för SQL som tillhandahålls av SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Åsidosätt standardsökvägen till målfilen återställning

Du kan åsidosätta sökvägen till målfilen återställning under återställningen genom att placera en JSON-fil som innehåller mappningen av databasfilen målsökväg för återställning. Skapa en `database_name.json` filen och placera den på plats *c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin\plugins\SQL*.

Innehållet i filen bör vara i formatet:
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

I det föregående innehållet, kan du få det logiska namnet för databasfilen genom att använda följande SQL-fråga:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Den här filen ska placeras innan du utlöser återställningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Backup för SQL Server-datorer (förhandsversion), [Azure Backup för SQL VM](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
