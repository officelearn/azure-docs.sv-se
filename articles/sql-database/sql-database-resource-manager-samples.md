---
title: Azure Resource Manager-mallar för SQL Database | Microsoft Docs
description: Använd Azure Resource Manager-mallar för att skapa och konfigurera Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 71bdfb28a251a815775dc77c55986a10ea550ef7
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770256"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure Resource Manager-mallar för Azure SQL Database

Med Azure Resource Manager-mallar kan du definiera din infrastruktur som kod och distribuera lösningar till Azure-molnet.

## <a name="single-database--elastic-pool"></a>Enkel databas och elastisk pool

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure SQL Database.

| |  |
|---|---|
| [Enkel databas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Den här Azure Resource Manager-mallen skapar en enkel Azure SQL-databas med en logisk server och konfigurerar brandväggsregler. |
| [Logisk server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Den här Azure Resource Manager-mallen skapar en logisk server för Azure SQL Database. |
| [Elastisk pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Med den här mallen kan du distribuera en ny elastisk pool med dess nya tillhörande SQL Server och nya SQL-databaser som den ska tilldelas. |
| [Redundansgrupper](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Den här mallen skapar två logiska Azure SQL-servrar, en SQL-databas och en redundansgrupp.|
| [Advanced Threat Protection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-advanced-threat-protection-server-policy) | Med den här mallen kan du distribuera en logisk Azure SQL-server med Advanced Threat Protection aktiverat och en valfri Azure SQL Database. SQL Advanced Threat Protection är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner.|
| [Hotidentifiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Med den här mallen kan du distribuera en logisk Azure SQL-server och en uppsättning Azure SQL-databaser med Hotidentifiering aktiverat, med en e-postadress för aviseringar för varje databas. Hotidentifiering är en del av erbjudandet för SQL Advanced Threat Protection (ATP). Det tillhandahåller ett säkerhetslager som reagerar på potentiella hot över SQL-servrar och databaser.|
| [Granskning för Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Med den här mallen kan du distribuera en logisk Azure SQL-server med granskning aktiverat för att skriva granskningsloggar till en bloblagring. Granskning för Azure SQL Database spårar databashändelser och skriver dem till en granskningslogg som kan placeras i ditt Azure Storage-konto, på din OMS-arbetsyta eller i Event Hubs.|
| [Granskning till Azure-händelsehubb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Med den här mallen kan du distribuera en Azure SQL-server med granskning aktiverat för att skriva granskningsloggar till en befintlig händelsehubb. För att kunna skicka granskningshändelser till en händelsehubb anger du granskningsinställningar med `Enabled` `State` och anger `IsAzureMonitorTargetEnabled` till `true`. Konfigurera även diagnostikinställningar med `SQLSecurityAuditEvents`-diagnostikloggkategorin i `master`-databasen (för granskning på servernivå). Granskning för Azure SQL Database och SQL Data Warehouse spårar databashändelser och skriver dem till en granskningslogg som kan placeras i ditt Azure Storage-konto, på din OMS-arbetsyta eller i Event Hubs.|
| [Azure-webbapp med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Det här exemplet skapar en kostnadsfri Azure-webbapp och en SQL-databas på tjänstnivån ”Basic” (Grundläggande).|
| [Azure-webbapp och Redis Cache med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Den här mallen skapar en webbapp, Redis Cache och SQL-databas i samma resursgrupp och skapar två anslutningssträngar i webbappen för SQL-databasen och Redis Cache.|
| [Importera data från bloblagring med hjälp av ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Den här Azure Resource Manager-mallen skapar Azure Data Factory V2 som kopierar data från Azure Blob Storage till SQL Database.|
| [HDInsight-kluster med en SQL-databas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Med den här mallen kan du skapa ett HDInsight-kluster, en SQL Database-server, en SQL-databas och två tabeller. Den här mallen används av artikeln om att [använda Sqoop med Hadoop i HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Azure-logikapp som kör en SQL-lagrad procedur enligt ett schema](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Med den här mallen kan du skapa en logikapp som ska köra en SQL-lagrad procedur enligt ett schema. Argument för proceduren kan placeras i brödtexten i mallen.|

## <a name="managed-instance"></a>Managed Instance

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure SQL Database – hanterad instans.

| |  |
|---|---|
| [Hanterad instans i ett nytt virtuellt nätverk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Den här Azure Resource Manager-mallen skapar ett nytt konfigurerat virtuellt Azure-nätverk och en hanterad instans i det virtuella nätverket. |
| [Nätverksmiljö för hanterad instans](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Den här distributionen skapar ett konfigurerat Azure-nätverk med två undernät – ett som dedikeras till dina hanterade instanser och ett annat där du kan placera andra resurser (till exempel virtuella datorer, App Service-miljöer osv.). Den här mallen skapar en korrekt konfigurerad nätverksmiljö där du kan distribuera hanterade instanser. |
| [Hanterad instans med P2S-anslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät, `ManagedInstance` och `GatewaySubnet`. Hanterad instans kommer att distribueras i undernätet ManagedInstance. Virtuell nätverksgateway skapas i undernätet `GatewaySubnet` och konfigureras för punkt-till-plats-VPN-anslutning. |
| [Hanterad instans med virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät, `ManagedInstance` och `Management`. Hanterad instans kommer att distribueras i undernätet `ManagedInstance`. Virtuell dator med den senaste versionen av SQL Server Management Studio (SSMS) kommer att distribueras i undernätet `Management`. |

