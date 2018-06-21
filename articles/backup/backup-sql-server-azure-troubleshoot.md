---
title: Azure-säkerhetskopiering felsökningsguide för SQL Server-datorer | Microsoft Docs
description: Felsökningsinformation för att säkerhetskopiera SQL Server-datorer till Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296213"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Felsöka säkerhetskopiera SQL Server på Azure

Den här artikeln innehåller felsökningsinformation för att skydda SQL Server virtuella datorer i Azure (förhandsversion).

## <a name="public-preview-limitations"></a>Allmänna begränsningar i förhandsversionen

Om du vill visa Public Preview-begränsningar finns i artikeln [säkerhetskopiera SQL Server-databas i Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

Konfigurera skydd för SQL Server-databasen på en virtuell dator i **AzureBackupWindowsWorkload** tillägget måste vara installerad på den virtuella datorn. Om du får felet, **UserErrorSQLNoSysadminMembership**, betyder det SQL-instansen inte har behörighet för säkerhetskopiering. Åtgärda felet genom att följa stegen i [ange behörigheter för icke-marketplace SQL VMs](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Felsökning av fel

Använd informationen i följande tabeller för att felsöka problem och fel påträffades när du skyddar SQL Server till Azure.

## <a name="backup-failures"></a>Fel vid säkerhetskopiering

Följande tabeller är ordnade efter felkod.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen stöder inte den begärda typ av säkerhetskopiering. | Inträffar när databasens Återställningsmodell inte tillåter den begärda typ av säkerhetskopiering. Felet kan inträffa i följande situationer: <br/><ul><li>En databas med en enkel Återställningsmodell tillåter inte loggsäkerhetskopiering.</li><li>Differentiella och logg säkerhetskopieringar tillåts inte för en databas.</li></ul>Mer information finns i [återställningsmodeller för SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentation. | Om loggsäkerhetskopieringen misslyckas av DB i enkla återställningsmodellen, försök med något av dessa alternativ:<ul><li>Om databasen är i enkelt återställningsläge, inaktivera säkerhetskopior.</li><li>Använd den [dokumentationen till SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) ändra databasens Återställningsmodell till fullständig eller Bulk loggas. </li><li> Om du har en princip som standard för att säkerhetskopiera flera databaser som inte kan ändras om du inte vill ändra återställningsmodellen och ignorera felet. Din fullständiga och differentiella säkerhetskopieringar fungerar per schema. Säkerhetskopior av kommer att hoppas över, som förväntas i det här fallet.</li></ul>Om det är en Master-databasen och du har konfigurerat differentiell eller loggen säkerhetskopiering, Använd någon av följande steg:<ul><li>Använd portalen för att ändra schemat för huvudservern säkerhetskopieringsprincip databasen till Full.</li><li>Om du har en princip som standard för att säkerhetskopiera flera databaser som inte kan ändras, ignorera felet. En fullständig säkerhetskopiering fungerar per schema. Differentiell eller loggen säkerhetskopieringar inträffa inte som förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Finns det [blogginlägg om säkerhetskopiera och återställa begränsningar](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) som körs samtidigt.| [Använda SQL Server Management Studio (SSMS) för att övervaka alla säkerhetskopieringsjobb.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Starta om åtgärden när motstridiga åtgärden misslyckas.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databas finns inte. | Databasen har antingen tagits bort eller bytt namn. | <ul><li>Kontrollera om databasen av misstag har tagits bort eller bytt namn.</li><li>Om databasen tagits bort av misstag, om du vill fortsätta säkerhetskopior, återställa databasen till den ursprungliga platsen.</li><li>Om du har tagit bort databasen och inte behöver framtida säkerhetskopieringar i Recovery Services-valvet på [stoppa säkerhetskopiering med ”ta bort/avinstallationsalternativ”](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn har säkerhetskopierats med en annan säkerhetskopieringslösning som trunkerar loggkedjan.|<ul><li>Kontrollera om en annan lösning för säkerhetskopiering eller skript. I så fall, stoppa den andra lösningen för säkerhetskopiering. </li><li>Om säkerhetskopieringen har en ad hoc-loggsäkerhetskopiering, startar du en fullständig säkerhetskopiering för att starta en ny loggkedjan. Ingen åtgärd krävs för schemalagda säkerhetskopieringar, som Azure Backup service utlöser automatiskt en fullständig säkerhetskopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup går inte att ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL-instansen. | Använd ytterligare information på menyn Azure portal fel för att begränsa de bakomliggande orsakerna. Referera till [SQL säkerhetskopiering felsökning](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) att åtgärda felet.<br/><ul><li>Om SQL standardinställningarna inte tillåter fjärranslutningar, ändra inställningarna. Referera till den länkarna för att ändra inställningarna nedan.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns problem med inloggning, finns i länkarna korrigera det nedan:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Första kompletta säkerhetskopian saknas för den här datakällan. | Fullständig säkerhetskopiering saknas för databasen. Loggen och differentiella säkerhetskopieringar överordnad till en fullständig säkerhetskopia, så fullständiga säkerhetskopieringar vidtas innan differentiell eller loggsäkerhetskopior. | Utlös en ad hoc-fullständig säkerhetskopiering.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att ta säkerhetskopiering eftersom transaktionsloggen för datakällan är full. | Databasutrymme för transaktionell loggen är full. | Åtgärda problemet genom att referera till den [dokumentationen till SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| SQL-databasen stöder inte den begärda typ av säkerhetskopiering. | Alltid på AG sekundära repliker stöder inte fullständig och differentiell säkerhetskopiering. | <ul><li>Om du utlöses en ad hoc-säkerhetskopiering, aktivera säkerhetskopiering på den primära noden.</li><li>Om säkerhetskopieringen har schemalagts av Grupprincip, kontrollera att den primära noden som har registrerats. Så här registrerar du noden [följer du stegen för att identifiera SQL Server-databasen](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Återställa fel

Följande felkoder som visas när återställning jobb misslyckas.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Databas med samma namn finns redan på målplatsen | Återställ målområdet har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål-databasen</li><li>Eller Använd alternativet force overwrite på sidan för återställning</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Återställningen misslyckades eftersom databasen inte kunde tas offline. | Medan du gör en återställning måste måldatabasen tas offline. Azure Backup kan inte överföra den här data offline. | Använd ytterligare information på menyn Azure portal fel för att begränsa de bakomliggande orsakerna. Mer information finns i [dokumentationen till SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta certifikatet med tumavtrycket på målet. | I Master-databasen på mål-instans har inte ett giltigt kryptering tumavtryck. | Importera giltig tumavtrycket används på källinstans på målinstansen. |

## <a name="registration-failures"></a>Registreringsfel

Följande felkoder gäller registreringsfel.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopiera inställningar för SQL Always On-Tillgänglighetsgruppen kan inte uppfyllas eftersom vissa noder för Tillgänglighetsgruppen inte är registrerad. | Noder som krävs för att utföra säkerhetskopieringar har registrerats inte eller inte kan nås. | <ul><li>Kontrollera att alla noder som krävs för att utföra säkerhetskopieringar för den här databasen är registrerade och felfri och försök sedan igen.</li><li>Ändra SQL Always On-Tillgänglighetsgruppen säkerhetskopiering inställningar.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQLServer-dator är antingen avstängd och inte tillgängliga för Azure Backup-tjänsten. | Virtuell dator är avstängd | Kontrollera att SQLServer körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup-tjänsten använder Azure VM-gästagent för att göra säkerhetskopiering men gästagenten är inte tillgänglig på målservern. | Gästagenten är inte aktiverad eller är inte felfri | [Installera virtuella datorns gästagent](../virtual-machines/extensions/agent-windows.md) manuellt. |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Backup för SQL Server-datorer (förhandsversion), [Azure Backup för SQL virtuella datorer (förhandsversion)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).