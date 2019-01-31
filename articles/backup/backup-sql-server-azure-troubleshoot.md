---
title: Azure felsökningsguide för säkerhetskopiering för SQL Server VM | Microsoft Docs
description: Felsökningsinformation för att säkerhetskopiera SQL Server-datorer till Azure.
services: backup
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/19/2018
ms.author: anuragm
ms.custom: ''
ms.openlocfilehash: 0d910269a16223c610e4606cdd6660cc5d43947f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296129"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Felsöka säkerhetskopiering av SQL Server på Azure

Den här artikeln innehåller felsökningsinformation för att skydda SQL Server-datorer på Azure (förhandsversion).

## <a name="public-preview-limitations"></a>Begränsningar för offentlig förhandsversion

Om du vill visa de offentliga begränsningarna i förhandsversionen finns i artikeln [säkerhetskopiera SQL Server-databas i Azure](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

Konfigurera skydd för en SQL Server-databas på en virtuell dator i **AzureBackupWindowsWorkload** tillägget måste vara installerad på den virtuella datorn. Om du får felet, **UserErrorSQLNoSysadminMembership**, betyder det SQL-instansen inte har behörighet för säkerhetskopiering. Om du vill åtgärda det här felet följer du stegen i [ange behörigheter för virtuella datorer inte finns i marketplace med SQL](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Felsöka fel

Använd informationen i följande tabeller för att felsöka problem och fel påträffades när du skyddar SQL Server till Azure.

## <a name="alerts"></a>Aviseringar

### <a name="backup-type-unsupported"></a>Typ av säkerhetskopiering stöds inte

| Severity | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | Aktuella inställningar för den här databasen stöder inte vissa typer av säkerhetskopieringstyper som finns i den associerade principen. | <li>**Master DB**: Endast en fullständig säkerhetskopiering databasåtgärd kan utföras i master-databasen; varken **differentiell** säkerhetskopiering eller transaktion **loggar** backup som möjligt. </li> <li>Alla databaser i **enkla återställningsmodellen** tillåter inte transaktionen **loggar** säkerhetskopia som ska vidtas.</li> | Ändra databasinställningarna så att alla säkerhetskopieringstyper i principen stöds. Du kan också ändra den aktuella principen om du vill inkludera endast säkerhetskopiering typer som stöds. I annat fall stöds inte säkerhetskopieringstyper kommer att åsidosättas vid schemalagd säkerhetskopiering eller säkerhetskopieringen misslyckas för ad hoc-säkerhetskopiering.


## <a name="backup-failures"></a>Fel vid säkerhetskopiering

Följande tabeller är ordnade efter felkod.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL database stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databasens Återställningsmodell inte tillåter den begärda säkerhetskopieringstypen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas med en enkel återställningsprocess tillåter inte loggsäkerhetskopiering.</li><li>Differentiella och loggbaserade säkerhetskopieringar tillåts inte för en databas.</li></ul>Mer information finns i den [återställningsmodeller för SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentation. | Om loggsäkerhetskopieringen misslyckas av DB i enkelt återställningsläge, prova något av följande alternativ:<ul><li>Om databasen är i enkelt återställningsläge, inaktivera loggsäkerhetskopior.</li><li>Använd den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) ändra databasens Återställningsmodell till Full eller Bulk Logged. </li><li> Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras om du inte vill ändra återställningsmodellen och ignorera felet. Din fullständiga och differentiella säkerhetskopieringar fungerar per schema. Säkerhetskopior av kommer att hoppas över, som förväntas i det här fallet.</li></ul>Om det är en Master-databasen och du har konfigurerat differentiell eller log säkerhetskopiering, Använd någon av följande steg:<ul><li>Använd portalen för att ändra säkerhetskopieringsprincipen schemat för huvudservern databasen till Full.</li><li>Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras kan du ignorera felet. Fullständiga säkerhetskopieringen fungerar per schema. Differentiell eller log säkerhetskopieringar göras inte som förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Se den [blogginlägg om hur du säkerhetskopierar och återställer begränsningar](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) som körs samtidigt.| [Använda SQL Server Management Studio (SSMS) för att övervaka säkerhetskopieringsjobb.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) När den hindrande åtgärden misslyckas, startar du om åtgärden.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databas finns inte. | Databasen har antingen tagits bort eller bytt namn. | <ul><li>Kontrollera om databasen av misstag har tagits bort eller bytt namn.</li><li>Om databasen tagits bort av misstag, om du vill fortsätta säkerhetskopiering, återställa databasen till den ursprungliga platsen.</li><li>Om du tog bort den och inte behöver framtida säkerhetskopior i Recovery Services-valv på [Avbryt säkerhetskopiering med ”ta bort/avinstallationsalternativ”](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms).</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhetskopieras med hjälp av en annan säkerhetskopieringslösning som trunkerar loggkedjan.|<ul><li>Kontrollera om en annan lösning för säkerhetskopiering eller skript används. I så fall kan du stoppa den andra lösningen för säkerhetskopiering. </li><li>Om säkerhetskopieringen har en ad hoc-loggsäkerhetskopiering, utlösa en fullständig säkerhetskopiering för att starta en ny loggkedjan. Ingen åtgärd krävs för schemalagda säkerhetskopieringar, eftersom Azure Backup-tjänsten kommer automatiskt att aktivera en fullständig säkerhetskopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup är inte kan ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL-instansen. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Referera till [SQL felsöka säkerhetskopiering av](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) åtgärda felet.<br/><ul><li>Om standardinställningarna för SQL inte tillåter fjärranslutningar, ändra inställningarna. Referera till den via länkarna för att ändra inställningarna nedan.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns problem med inloggningen, referera till den via länkarna kan åtgärdas nedan:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Första fullständig säkerhetskopiering saknas för den här datakällan. | Fullständig säkerhetskopiering saknas för databasen. Logg- och differentiella säkerhetskopieringar överordnad till en fullständig säkerhetskopia, så att fullständiga säkerhetskopior måste du vidta innan du aktiverar RDC eller säkerhetskopior. | Utlös en ad hoc fullständig säkerhetskopiering.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att säkerhetskopiera eftersom transaktionsloggen för datakällan är full. | Transaktionell loggutrymmet för databasen är full. | Om du vill åtgärda problemet genom att referera till den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| Den här SQL database stöder inte den begärda säkerhetskopieringstypen. | Alltid på AG sekundära repliker stöder inte fullständiga och differentiella säkerhetskopieringar. | <ul><li>Om du har utlöst en ad hoc-säkerhetskopiering, utlösa säkerhetskopior på den primära noden.</li><li>Om säkerhetskopieringen har schemalagts av Grupprincip, kontrollera att den primära noden har registrerats. Att registrera noden [följer du stegen för att identifiera en SQL Server-databas](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> |

## <a name="restore-failures"></a>Återställningsfel

Följande felkoder som visas när återställning jobb misslyckas.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Databasen med samma namn finns redan på målplatsen | Återställningsmål mål har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål-databasen</li><li>Du kan också använda alternativet force överskrivning på sidan för återställning</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du gör en återställning måldatabasen som behöver tas offline. Azure Backup kan inte ta med dessa data offline. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Mer information finns i den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta servercertifikat med tumavtryck på målet. | Huvudservern databasen på mål-instansen har inte ett giltigt tumavtryck. | Importera giltigt certifikat-tumavtrycket används på målinstansen på målinstansen. |

## <a name="registration-failures"></a>Registreringsfel

Följande felkoder finns för registreringsfel.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL Always On Availability Group kan inte tillämpas eftersom vissa noder i Tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhetskopieringar har registrerats inte eller inte kan nås. | <ul><li>Kontrollera att alla noder som krävs för att utföra säkerhetskopieringar av den här databasen är registrerade och felfria och försök sedan igen.</li><li>Ändra SQL Always On Availability Group inställning för säkerhetskopiering.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQLServer-dator är antingen avstängd eller inte tillgänglig för Azure Backup-tjänsten. | Virtuella datorn stängs av | Se till att SQLServer körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup-tjänsten använder Virtuella Azure-gästagenten för säkerhetskopiering men gästagenten är inte tillgängliga på målservern. | Gästagenten är inte aktiverat eller är skadad | [Installera virtuella datorns gästagent](../virtual-machines/extensions/agent-windows.md) manuellt. |

## <a name="configure-backup-failures"></a>Konfigurera Backup-fel

Följande fel är koder för Konfigurera säkerhetskopiering fel.

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Automatiskt skydd avsett har antingen tagits bort eller är inte längre giltig. | När du aktiverar automatiskt skydd på en SQL-instans **Konfigurera säkerhetskopiering** uppgifter har körts för alla databaser i instansen. Om du inaktiverar automatiskt skydd medan jobben körs, kommer **pågår** jobb avbryts med den här felkoden. | Aktivera automatiskt skydd igen att skydda alla återstående databaser. |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Backup för SQL Server-datorer (förhandsversion), [Azure Backup för SQL-datorer (förhandsversion)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
