---
title: "Flytta data till en Azure SQL Database för Azure Machine Learning | Microsoft Docs"
description: "Skapa SQL-tabellen och Läs in data till SQL-tabell"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: 323861d078e9beeb197333dc7e2d0314014dfdb0
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2017
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Flytta data till en Azure SQL Database för Azure Machine Learning
Det här avsnittet beskrivs alternativen för att flytta data från flata filer (CSV eller TVS format) eller data som lagras i en lokal SQL Server till en Azure SQL-databas. Dessa uppgifter för att flytta data till molnet är en del av Team av vetenskapliga data.

Ett avsnitt som visar alternativen för att flytta data till en lokal SQL Server för Machine Learning finns [flytta data till SQL Server på en virtuell dator i Azure](move-sql-server-virtual-machine.md).

Följande **menyn** länkar till avsnitt som beskriver hur du mata in data i mål-miljöer där data kan lagras och behandlas under Team Data vetenskap processen (TDSP).

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

I följande tabell sammanfattas alternativ för att flytta data till en Azure SQL Database.

| <b>KÄLLA</b> | <b>MÅL: Azure SQL-databas</b> |
| --- | --- |
| <b>Flat-fil (CSV eller TVS formaterad)</b> |<a href="#bulk-insert-sql-query">Bulk Insert SQL-fråga |
| <b>Lokal SQLServer</b> |1. <a href="#export-flat-file">Exportera till Flat-fil<br> 2. <a href="#insert-tables-bcp">Migreringsguiden för SQL-databas<br> 3. <a href="#db-migration">Databasen tillbaka in och återställa<br> 4. <a href="#adf">Azure Data Factory |

## <a name="prereqs"></a>Förhandskrav
De procedurer som beskrivs här måste du ha:

* En **Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En **Azure storage-konto**. Du kan använda ett Azure storage-konto för att lagra data i den här kursen. Om du inte har ett Azure storage-konto finns i [skapa ett lagringskonto](../../storage/common/storage-create-storage-account.md#create-a-storage-account) artikel. När du har skapat lagringskontot som du behöver hämta nyckeln konto används för åtkomst till lagringen. Se [hantera åtkomstnycklar för lagring](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Åtkomst till en **Azure SQL Database**. Om du måste ställa in en Azure SQL Database [komma igång med Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) innehåller information om hur du etablerar en ny instans av en Azure SQL Database.
* Installerat och konfigurerat **Azure PowerShell** lokalt. Instruktioner finns i [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

**Data**: migrering processer kan visas med hjälp av den [NYC Taxi dataset](http://chriswhong.com/open-data/foil_nyc_taxi/). NYC Taxi datamängden innehåller information om resa data och mässor och är tillgängligt på Azure-blobblagring: [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Ett exempel och en beskrivning av dessa filer finns i [NYC Taxi resor Dataset beskrivning](sql-walkthrough.md#dataset).

Du kan anpassa de förfaranden som beskrivs här till en uppsättning med dina egna data eller Följ stegen som beskrivs med NYC Taxi dataset. Överför NYC Taxi dataset till din lokala SQL Server-databas genom att följa proceduren som beskrivs i [Bulk importera Data till SQL Server-databas](sql-walkthrough.md#dbload). Dessa instruktioner är för en SQL Server på en virtuell dator i Azure, men proceduren för att ladda upp till den lokala SQL Server är samma.

## <a name="file-to-azure-sql-database"></a>Flytta data från en flat filkälla till en Azure SQL database
Data i flat-filer (CSV eller TVS formaterad) kan flyttas till en Azure SQL-databas med en Bulk Insert SQL-fråga.

### <a name="bulk-insert-sql-query"></a>Bulk Insert SQL-fråga
Anvisningarna för proceduren med hjälp av Bulk Insert SQL-frågan är samma som beskrivs i avsnitten för att flytta data från en flat filkälla till SQL Server på en Azure VM. Mer information finns i [Bulk Insert SQL-frågan](move-sql-server-virtual-machine.md#insert-tables-bulkquery).

## <a name="sql-on-prem-to-sazure-sql-database"></a>Flytta Data från lokala SQL Server till en Azure SQL database
Om datakällan finns i en lokal SQL Server, finns det olika möjligheter för att flytta data till en Azure SQL database:

1. [Exportera till Flat-fil](#export-flat-file)
2. [Migreringsguiden för SQL-databas](#insert-tables-bcp)
3. [Databasen tillbaka in och återställa](#db-migration)
4. [Azure Data Factory](#adf)

Steg för de första tre liknar de avsnitten i [flytta data till SQL Server på en virtuell dator i Azure](move-sql-server-virtual-machine.md) som omfattar samma sätt. Länkar till relevanta avsnitt i det avsnittet finns i följande instruktioner.

### <a name="export-flat-file"></a>Exportera till Flat-fil
Steg för den här exporten till en flat-fil är samma som beskrivs i [exportera till Flat fil](move-sql-server-virtual-machine.md#export-flat-file).

### <a name="insert-tables-bcp"></a>Migreringsguiden för SQL-databas
Steg för att använda guiden SQL-databas är samma som beskrivs i [Migreringsguiden för SQL-databasen](move-sql-server-virtual-machine.md#sql-migration).

### <a name="db-migration"></a>Databasen tillbaka in och återställa
Steg för att använda databasen säkerhetskopiera och Återställ liknar de som beskrivs i [databasen tillbaka in och återställa](move-sql-server-virtual-machine.md#sql-backup).

### <a name="adf"></a>Azure Data Factory
Proceduren för att flytta data till en Azure SQL database med Azure Data Factory (ADM) finns i avsnittet [flytta data från en lokal SQLServer till SQL Azure med Azure Data Factory](move-sql-azure-adf.md). Det här avsnittet beskrivs hur du flyttar data från en lokal SQL Server-databas till en Azure SQL database via Azure Blob Storage med hjälp av ADF.

Överväg att använda ADF när data ska migreras kontinuerligt i ett hybridscenario som har åtkomst till både lokalt och molnresurser och när data är överförd eller måste ändras eller har affärslogik som lagts till när migreras. ADF möjliggör schemaläggningen och övervakning av jobb med hjälp av enkla JSON-skript som hanterar flödet av data regelbundet. ADF har även andra funktioner som stöd för komplex.
