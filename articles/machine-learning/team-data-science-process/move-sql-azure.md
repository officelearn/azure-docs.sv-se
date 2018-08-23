---
title: Flytta data till en Azure SQL Database för Azure Machine Learning | Microsoft Docs
description: Skapa SQL-tabell och läsa in data till SQL-tabell
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: d3a7bc59654ace8dbe647b5a780985fa1556f1da
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42060970"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytta data till en Azure SQL Database för Azure Machine Learning
Det här avsnittet beskrivs alternativen för att flytta data från flata filer (CSV- eller TSV-format) eller från data som lagras i en lokal SQL Server till en Azure SQL-databas. Dessa uppgifter för att flytta data till molnet är en del av Team Data Science Process.

Ett avsnitt som visar alternativ för att flytta data till en lokal SQL Server för Machine Learning, se [flytta data till SQL Server på virtuella Azure-datorer](move-sql-server-virtual-machine.md).

Följande **menyn** länkar till avsnitt som beskriver hur du mata in data i målmiljöer där data kan lagras och bearbetas under Team Data Science Process (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

I följande tabell sammanfattas alternativen för att flytta data till en Azure SQL Database.

| <b>KÄLLA</b> | <b>MÅL: Azure SQL-databas</b> |
| --- | --- |
| <b>Flat fil (CSV- eller TSV formaterad)</b> |[Bulk Insert SQL-fråga](#bulk-insert-sql-query) |
| <b>En lokal SQLServer</b> |1.[exportera till Flat fil](#export-flat-file)<br> 2. [Migreringsguide för SQL-databas](#insert-tables-bcp)<br> 3. [Databasen tillbaka upp och återställa](#db-migration)<br> 4. [Azure Data Factory](#adf) |

## <a name="prereqs"></a>Förhandskrav
Procedurerna som beskrivs här kräver att du har:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du kan använda ett Azure storage-konto för att lagra data i den här självstudien. Om du inte har ett Azure storage-konto kan du läsa den [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md) artikeln. När du har skapat lagringskontot kan behöva du hämta den kontonyckel som används för att komma åt lagringsutrymmet. Se [hantera dina lagringsåtkomstnycklar](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Åtkomst till en **Azure SQL Database**. Om du måste konfigurera en Azure SQL Database, [komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) innehåller information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Anvisningar finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

**Data**: migrering processer är visas med hjälp av den [NYC Taxi datauppsättning](http://chriswhong.com/open-data/foil_nyc_taxi/). NYC Taxi-datauppsättning innehåller information om resedata och mässor och är tillgängligt på Azure blob-lagring: [NYC Taxiinformation](http://www.andresmh.com/nyctaxitrips/). Ett exempel och en beskrivning av dessa filer finns i [NYC Taxi och RETUR datauppsättning beskrivning](sql-walkthrough.md#dataset).

Du kan anpassa de förfaranden som beskrivs här till en uppsättning med dina egna data eller följer du stegen som beskrivs med hjälp av NYC Taxi-datauppsättningen. Om du vill överföra NYC Taxi-datauppsättning till din lokala SQL Server-databas, följer du proceduren som beskrivs i [Bulk importera Data till SQL Server-databas](sql-walkthrough.md#dbload). Dessa instruktioner är för en SQL Server på en Azure-dator, men proceduren för att ladda upp till en lokal SQL Server är samma.

## <a name="file-to-azure-sql-database"></a> Flytta data från en källa för platt fil till en Azure SQL database
Data i flat-filer (CSV- eller TSV formaterad) kan flyttas till en Azure SQL-databas via en Bulk Insert SQL-fråga.

### <a name="bulk-insert-sql-query"></a> Bulk Insert SQL-fråga
Stegen för att proceduren med hjälp av Bulk Insert SQL-frågan är samma som beskrivs i avsnitten för att flytta data från en flat fil-källa till SQL Server på en Azure VM. Mer information finns i [Bulk Insert SQL-fråga](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a> Flytta Data från en lokal SQL Server till en Azure SQL database
Om datakällan lagras i en lokal SQL Server, finns det olika möjligheter för att flytta data till en Azure SQL database:

1. [Exportera till Flat fil](#export-flat-file)
2. [Migreringsguide för SQL-databas](#insert-tables-bcp)
3. [Databasen tillbaka upp och återställa](#db-migration)
4. [Azure Data Factory](#adf)

Stegen för de första tre är mycket lik dessa avsnitt i [flytta data till SQL Server på virtuella Azure-datorer](move-sql-server-virtual-machine.md) som omfattar samma sätt. Länkar till relevanta avsnitt i avsnittet finns i följande anvisningar.

### <a name="export-flat-file"></a>Exportera till Flat fil
Stegen för den här exporterar du en platt fil är samma som beskrivs i [exportera till Flat fil](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Migreringsguide för SQL-databas
Steg för att använda guiden SQL-databas är samma som beskrivs i [SQL Database Migration Wizard](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databasen tillbaka upp och återställa
Steg för att använda databasen säkerhetskopiera och Återställ liknar de som beskrivs i [databasen tillbaka upp och återställa](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Proceduren för att flytta data till en Azure SQL database med Azure Data Factory (ADF) finns i avsnittet [flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory](move-sql-azure-adf.md). Det här avsnittet visar hur du flyttar data från en lokal SQL Server-databas till en Azure SQL database via Azure Blob Storage med hjälp av ADF.

Överväg att använda ADF när data ska migreras kontinuerligt i ett hybridscenario som har åtkomst till både lokalt och i molnresurser och när data är överförda eller behöver ändras eller ha affärslogik som läggs till det när du håller på att migreras. ADF tillåter schemaläggning och övervakning av jobb med hjälp av enkla JSON-skript som hanterar överföringen av data på regelbunden basis. ADF har även andra funktioner, till exempel stöd för komplexa åtgärder.
