---
title: Flytta data till en Azure SQL Database - Team Data Science Process
description: Flytta data från flata filer (CSV-eller TSV-format) eller från data som lagras i en lokal SQL Server till en Azure SQL Database.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722466"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytta data till en Azure SQL Database för Azure Machine Learning

Den här artikeln beskriver alternativen för att flytta data antingen från flata filer (CSV-eller TSV-format) eller från data som lagras i en lokal SQL Server till en Azure SQL Database. Dessa uppgifter för att flytta data till molnet är en del av Team Data Science Process.

Ett avsnitt som visar alternativ för att flytta data till en lokal SQL Server för Machine Learning, se [flytta data till SQL Server på virtuella Azure-datorer](move-sql-server-virtual-machine.md).

I följande tabell sammanfattas alternativen för att flytta data till en Azure SQL Database.

| <b>KÄLLA</b> | <b>MÅL: Azure SQL-databas</b> |
| --- | --- |
| <b>Flat fil (CSV- eller TSV formaterad)</b> |[Bulk Insert SQL-fråga](#bulk-insert-sql-query) |
| <b>En lokal SQLServer</b> |1.[exportera till Flat fil](#export-flat-file)<br> 2. [guiden SQL Database migrering](#insert-tables-bcp)<br> 3. [säkerhetskopiera och Återställ databasen](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Förhandskrav
Procedurerna som beskrivs här kräver att du har:

* En **Azure-prenumeration**. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du kan använda ett Azure storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure storage-konto kan du läsa den [skapa ett lagringskonto](../../storage/common/storage-account-create.md) artikeln. När du har skapat lagringskontot kan behöva du hämta den kontonyckel som används för att komma åt lagringsutrymmet. Se [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).
* Åtkomst till en **Azure SQL Database**. Om du måste konfigurera en Azure SQL Database, [komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) innehåller information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Anvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

**Data**: migrering processer är visas med hjälp av den [NYC Taxi datauppsättning](https://chriswhong.com/open-data/foil_nyc_taxi/). NYC Taxi-datauppsättning innehåller information om resedata och mässor och är tillgängligt på Azure blob-lagring: [NYC Taxiinformation](https://www.andresmh.com/nyctaxitrips/). Ett exempel och en beskrivning av dessa filer finns i [NYC Taxi och RETUR datauppsättning beskrivning](sql-walkthrough.md#dataset).

Du kan anpassa de förfaranden som beskrivs här till en uppsättning med dina egna data eller följer du stegen som beskrivs med hjälp av NYC Taxi-datauppsättningen. Om du vill överföra NYC Taxi-datauppsättning till din lokala SQL Server-databas, följer du proceduren som beskrivs i [Bulk importera Data till SQL Server-databas](sql-walkthrough.md#dbload). Dessa instruktioner är för en SQL Server på en Azure-dator, men proceduren för att ladda upp till en lokal SQL Server är samma.

## <a name="file-to-azure-sql-database"></a>Flytta data från en platt fil källa till en Azure SQL Database
Data i flata filer (CSV eller TSV formaterade) kan flyttas till en Azure SQL Database med hjälp av en SQL-fråga för Mass inmatning.

### <a name="bulk-insert-sql-query"></a> Bulk Insert SQL-fråga
Stegen för proceduren med hjälp av Mass infogning av SQL-fråga liknar riktningarna för att flytta data från en platt fil källa till SQL Server på en virtuell Azure-dator. Mer information finns i [Bulk Insert SQL-fråga](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Flytta data från lokala SQL Server till en Azure SQL Database
Om käll informationen lagras i en lokal SQL Server finns det flera möjligheter att flytta data till en Azure SQL Database:

1. [Exportera till Flat fil](#export-flat-file)
2. [Migreringsguide för SQL-databas](#insert-tables-bcp)
3. [Databasen tillbaka upp och återställa](#db-migration)
4. [Azure Data Factory](#adf)

Stegen för de första tre liknar dessa avsnitt i [Flytta data till SQL Server på en virtuell Azure-dator](move-sql-server-virtual-machine.md) som behandlar samma procedurer. Länkar till relevanta avsnitt i avsnittet finns i följande anvisningar.

### <a name="export-flat-file"></a>Exportera till Flat fil
Stegen för att exportera till en platt fil liknar de instruktioner som beskrivs i [Exportera till Flate-fil](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Migreringsguide för SQL-databas
Stegen för att använda guiden SQL Database migrering liknar de riktningar som beskrivs i [guiden SQL Database migrering](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databasen tillbaka upp och återställa
Stegen för att använda säkerhets kopiering och återställning av databasen liknar de riktningar som anges i [säkerhets kopiering och återställning av databasen](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Lär dig hur du flyttar data till en Azure SQL Database med Azure Data Factory (ADF) i det här avsnittet, [Flytta data från en lokal SQL Server till SQL Azure med Azure Data Factory](move-sql-azure-adf.md). Det här avsnittet visar hur du använder ADF för att flytta data från en lokal SQL Server databas till en Azure SQL Database via Azure Blob Storage.

Överväg att använda ADF när data måste migreras kontinuerligt med hybrid lokala och molnbaserade källor.  ADF hjälper också till när data behöver transformeras eller behöver en ny affärs logik under migreringen. ADF tillåter schemaläggning och övervakning av jobb med hjälp av enkla JSON-skript som hanterar överföringen av data på regelbunden basis. ADF har även andra funktioner, till exempel stöd för komplexa åtgärder.
