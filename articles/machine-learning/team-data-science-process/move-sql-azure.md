---
title: Flytta data till en Azure SQL Database - Team Data Science Process
description: Flytta data från platta filer (CSV- eller TSV-format) eller från data som lagras i en lokal SQL Server till en Azure SQL-databas.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722466"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytta data till en Azure SQL Database för Azure Machine Learning

I den här artikeln beskrivs alternativen för att flytta data antingen från platta filer (CSV- eller TSV-format) eller från data som lagras i en lokal SQL Server till en Azure SQL-databas. Dessa uppgifter för att flytta data till molnet är en del av teamdatavetenskapsprocessen.

Ett avsnitt som beskriver alternativen för att flytta data till en lokal SQL Server for Machine Learning finns i [Flytta data till SQL Server på en virtuell Azure-dator](move-sql-server-virtual-machine.md).

I följande tabell sammanfattas alternativen för att flytta data till en Azure SQL-databas.

| <b>Källkod</b> | <b>DESTINATION: Azure SQL-databas</b> |
| --- | --- |
| <b>Platt fil (CSV- eller TSV-formaterad)</b> |[SQL-fråga för massinfogning](#bulk-insert-sql-query) |
| <b>Lokalt SQL Server</b> |1.[Exportera till platt fil](#export-flat-file)<br> 2. [Guiden MigRERING AV SQL-databas](#insert-tables-bcp)<br> 3. [Databas säkerhetskopiera och återställa](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>Krav
De procedurer som beskrivs här kräver att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* Ett **Azure-lagringskonto**. Du använder ett Azure-lagringskonto för att lagra data i den här självstudien. Om du inte har ett Azure-lagringskonto läser du artikeln [Skapa ett lagringskonto.](../../storage/common/storage-account-create.md) När du har skapat lagringskontot måste du hämta kontonyckeln som används för att komma åt lagringen. Se [Hantera åtkomstnycklar för lagringskonto.](../../storage/common/storage-account-keys-manage.md)
* Åtkomst till en **Azure SQL-databas**. Om du måste konfigurera en Azure SQL-databas innehåller [Komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) information om hur du etablerar en ny instans av en Azure SQL-databas.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

**Data**: Migreringsprocesserna demonstreras med hjälp av [NYC Taxi-datauppsättningen](https://chriswhong.com/open-data/foil_nyc_taxi/). Den NYC Taxi datauppsättning innehåller information om resa data och mässor och är tillgänglig på Azure blob lagring: [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/). Ett exempel och beskrivning av dessa filer finns i [NYC Taxi Trips Dataset Beskrivning](sql-walkthrough.md#dataset).

Du kan antingen anpassa procedurerna som beskrivs här till en uppsättning med dina egna data eller följa stegen enligt beskrivningen med hjälp av NYC Taxi-datauppsättningen. Om du vill överföra NYC Taxi-datauppsättningen till din lokala SQL Server-databas följer du proceduren som beskrivs i [Massimportdata till SQL Server Database](sql-walkthrough.md#dbload). Dessa instruktioner gäller för en SQL Server på en virtuell Azure-dator, men proceduren för överföring till den lokala SQL Server är densamma.

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a>Flytta data från en platt filkälla till en Azure SQL-databas
Data i platta filer (CSV- eller TSV-formaterad) kan flyttas till en Azure SQL-databas med hjälp av en SQL-fråga för massinfogning.

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>SQL-fråga för massinfogning
Stegen för proceduren med hjälp av SQL Query för massinfogning liknar anvisningarna för att flytta data från en platt filkälla till SQL Server på en virtuell Azure-dator. Mer information finns i [SQL Query för massinfogning](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="moving-data-from-on-premises-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a>Flytta data från lokal SQL Server till en Azure SQL-databas
Om källdata lagras i en lokal SQL Server finns det olika möjligheter att flytta data till en Azure SQL-databas:

1. [Exportera till platt fil](#export-flat-file)
2. [Guiden Migrering av SQL-databas](#insert-tables-bcp)
3. [Databasen säkerhetskopierar och återställer](#db-migration)
4. [Azure Data Factory](#adf)

Stegen för de tre första liknar de avsnitt i [Flytta data till SQL Server på en virtuell Azure-dator](move-sql-server-virtual-machine.md) som täcker samma procedurer. Länkar till lämpliga avsnitt i det avsnittet finns i följande instruktioner.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportera till platt fil
Stegen för den här exporten till en platt fil liknar de anvisningar som anges i [Exportera till platt fil](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>Guiden Migrering av SQL-databas
Stegen för att använda guiden Migrering av SQL-databas liknar de anvisningar som beskrivs i [guiden Migrering](move-sql-server-virtual-machine.md#sql-migration)i SQL Database .

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>Databasen säkerhetskopierar och återställer
Stegen för att använda säkerhetskopiering och återställning av databasen liknar de riktningar som anges i [Säkerhetskopiering och återställning](move-sql-server-virtual-machine.md#sql-backup)av databasen .

### <a name="azure-data-factory"></a><a name="adf"></a>Azure Data Factory
Lär dig hur du flyttar data till en Azure SQL-databas med Azure Data Factory (ADF) i det här avsnittet, [Flytta data från en lokal SQL-server till SQL Azure med Azure Data Factory](move-sql-azure-adf.md). Det här avsnittet visar hur du använder ADF för att flytta data från en lokal SQL Server-databas till en Azure SQL-databas via Azure Blob Storage.

Överväg att använda ADF när data måste migreras kontinuerligt med lokala hybrid- och molnkällor.  ADF hjälper också när data behöver omvandlingar, eller behöver ny affärslogik under migreringen. ADF möjliggör schemaläggning och övervakning av jobb med hjälp av enkla JSON-skript som hanterar förflyttning av data regelbundet. ADF har också andra funktioner, till exempel stöd för komplexa åtgärder.
