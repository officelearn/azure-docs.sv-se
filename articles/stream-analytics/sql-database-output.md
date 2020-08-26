---
title: Azure SQL Database utdata från Azure Stream Analytics
description: I den här artikeln beskrivs Azure SQL Database som utdata för Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d61cad0be25f5aa1a4c63bf3dc128196ad80fdad
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875850"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure SQL Database utdata från Azure Stream Analytics

Du kan använda [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) som utdata för data som är i relation till typ eller för program som är beroende av innehåll som finns i en Relations databas. Azure Stream Analytics jobb skriver till en befintlig tabell i SQL Database. Tabell schemat måste exakt matcha fälten och deras typer i jobbets utdata. Du kan också ange [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) som utdata via alternativet SQL Database utdata. Information om hur du kan förbättra Skriv data flödet finns i artikeln [Stream Analytics med Azure SQL Database som utdata](stream-analytics-sql-output-perf.md) .

Du kan också använda en [hanterad Azure SQL-instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) som utdata. Du måste [Konfigurera den offentliga slut punkten i SQL-hanterad instans](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) och manuellt konfigurera följande inställningar i Azure Stream Analytics. Den virtuella Azure-datorn som kör SQL Server med en databas ansluten stöds också genom att konfigurera inställningarna manuellt nedan.

## <a name="output-configuration"></a>Konfiguration av utdata

I följande tabell visas egenskaps namnen och deras beskrivning för att skapa en SQL Database utdata.

| Egenskapsnamn | Beskrivning |
| --- | --- |
| Utdataalias |Ett eget namn som används i frågor för att dirigera frågeresultatet till den här databasen. |
| Databas | Namnet på databasen dit du skickar dina utdata. |
| Servernamn | Det logiska SQL Server-namnet eller namnet på den hanterade instansen. För SQL-hanterad instans måste du ange port 3342. Till exempel *sampleserver. public. Database. Windows. net, 3342* |
| Användarnamn | Det användar namn som har Skriv behörighet till databasen. Stream Analytics stöder endast SQL-autentisering. |
| Lösenord | Lösen ordet för att ansluta till databasen. |
| Tabell | Tabell namnet där utdata skrivs. Tabell namnet är Skift läges känsligt. Schemat för den här tabellen ska exakt matcha det antal fält och deras typer som jobbets utdata genererar. |
|Ärv partitionsschema| Ett alternativ för att ärva partitionerings schema i föregående fråga-steg för att aktivera helt parallell topologi med flera skrivare till tabellen. Mer information finns i [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).|
|Max antal batchar| Den rekommenderade övre gränsen för antalet poster som skickas med varje Mass infognings transaktion.|

Det finns två kort som aktiverar utdata från Azure Stream Analytics till Azure Synapse Analytics (tidigare SQL Data Warehouse): SQL Database och Azure Synapse. Vi rekommenderar att du väljer Azure Synapse Analytics-kortet i stället för SQL Database kort om något av följande villkor är uppfyllt:

* **Data flöde**: om det förväntade data flödet nu eller i framtiden är större än 10 MB/SEK, använder du alternativet för Azure Synapse-utdata för bättre prestanda.

* **Indata-partitioner**: om du har åtta eller fler indata-partitioner använder du alternativet för Azure Synapse-utdata för att få bättre skalbarhet.

## <a name="partitioning"></a>Partitionering

Partitionering måste aktive ras och baseras på PARTITION BY-satsen i frågan. När alternativet Ärv partitionering är aktiverat följer den inpartitionering för [fullständiga kan göras parallella-frågor](stream-analytics-scale-jobs.md). Mer information om hur du uppnår bättre Skriv data flödes prestanda när du läser in data i Azure SQL Database finns [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md).

## <a name="output-batch-size"></a>Batchstorlek för utdata

Du kan konfigurera Max storleken för meddelanden genom att använda **maximalt antal batchar**. Standardvärdet är 10 000 och standardvärdet är 100 rader per enskild Mass infogning. Mer information finns i [begränsningar för Azure SQL](../sql-database/sql-database-resource-limits.md). Varje batch infogas från början med maximalt antal batchar. Batch är uppdelad i mitten (till lägsta antal batchar) baserat på nya försök att köra fel från SQL.

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Azure CLI](quick-create-azure-cli.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med en ARM-mall](quick-create-azure-resource-manager.md)
* [Snabb start: skapa ett Stream Analytics jobb med Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb med hjälp av Visual Studio](stream-analytics-quick-create-vs.md)
* [Snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](quick-create-vs-code.md)
