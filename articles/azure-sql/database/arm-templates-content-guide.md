---
title: Azure Resource Manager-mallar
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Använd Azure Resource Manager mallar för att skapa och konfigurera Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053293"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Azure Resource Manager mallar för Azure SQL Database & SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure Resource Manager mallar kan du definiera din infrastruktur som kod och distribuera dina lösningar till Azure-molnet för din Azure SQL Database och Azure SQL-hanterad instans.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Följande tabell innehåller länkar till Azure Resource Manager-mallar för Azure SQL Database.

| |  |
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Den här Azure Resource Manager-mallen skapar en enda databas i Azure SQL Database och konfigurerar IP-brandvägg på server nivå. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Den här Azure Resource Manager-mallen skapar en server för Azure SQL Database. |
| [Elastisk pool](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Med den här mallen kan du distribuera en elastisk pool och tilldela databaser till den. |
| [Redundansgrupper](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Den här mallen skapar två servrar, en enda databas och en failover-grupp i Azure SQL Database.|
| [Hot identifiering](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Med den här mallen kan du distribuera en server och en uppsättning databaser med hot identifiering aktive rad, med en e-postadress för aviseringar för varje databas. Hot identifiering är en del av erbjudandet för SQL Advanced Threat Protection (ATP) och ger ett säkerhets lager som svarar på potentiella hot över servrar och databaser.|
| [Granskning för Azure Blob Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Med den här mallen kan du distribuera en server med granskning aktiverat för att skriva gransknings loggar till en blob-lagring. Granskning för Azure SQL Database spårar databashändelser och skriver dem till en granskningslogg som kan placeras i ditt Azure Storage-konto, på din OMS-arbetsyta eller i Event Hubs.|
| [Granskning till Azure-händelsehubb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Med den här mallen kan du distribuera en server med granskning aktiverat för att skriva gransknings loggar till en befintlig Händelsehubben. För att kunna skicka gransknings händelser till Händelsehubben anger du gransknings inställningar med `Enabled` `State` och ange `IsAzureMonitorTargetEnabled` som `true` . Konfigurera också diagnostikinställningar med `SQLSecurityAuditEvents` logg kategori i `master` databasen (för granskning på betjänande nivå). Granskning spårar databas händelser och skriver dem till en Gransknings logg som kan placeras i ditt Azure Storage-konto, OMS-arbetsyta eller Event Hubs.|
| [Azure-webbapp med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Det här exemplet skapar en kostnads fri Azure-webbapp och en databas i Azure SQL Database på tjänst nivån "Basic".|
| [Azure-webbapp och Redis Cache med SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Den här mallen skapar en webbapp, Redis Cache och SQL-databas i samma resursgrupp och skapar två anslutningssträngar i webbappen för SQL-databasen och Redis Cache.|
| [Importera data från bloblagring med hjälp av ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Den här Azure Resource Manager-mallen skapar Azure Data Factory V2 som kopierar data från Azure Blob Storage till SQL Database.|
| [HDInsight-kluster med en SQL-databas](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Med den här mallen kan du skapa ett HDInsight-kluster, en logisk SQL-Server, en databas och två tabeller. Den här mallen används av artikeln om att [använda Sqoop med Hadoop i HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Azure-logikapp som kör en SQL-lagrad procedur enligt ett schema](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Med den här mallen kan du skapa en logikapp som ska köra en SQL-lagrad procedur enligt ett schema. Argument för proceduren kan placeras i brödtexten i mallen.|

## <a name="azure-sql-managed-instance"></a>[Hanterad Azure SQL-instans](#tab/managed-instance)

Följande tabell innehåller länkar till Azure Resource Manager mallar för Azure SQL-hanterad instans.

| |  |
|---|---|
| [SQL-hanterad instans i ett nytt VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Den här Azure Resource Manager-mallen skapar en ny konfigurerad Azure VNet-och SQL-hanterad instans i VNet. |
| [Nätverks miljö för SQL-hanterad instans](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Den här distributionen skapar en konfigurerad Azure-Virtual Network med två undernät – en som kommer att vara dedikerad till dina SQL-hanterade instanser och en annan där du kan placera andra resurser (till exempel virtuella datorer, App Service miljöer osv.). Den här mallen skapar en korrekt konfigurerad nätverks miljö där du kan distribuera SQL-hanterade instanser. |
| [SQL-hanterad instans med P2S-anslutning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät, `ManagedInstance` och `GatewaySubnet`. SQL-hanterad instans kommer att distribueras i ManagedInstance-undernätet. Virtuell nätverksgateway skapas i undernätet `GatewaySubnet` och konfigureras för punkt-till-plats-VPN-anslutning. |
| [SQL-hanterad instans med virtuell dator](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Den här distributionen skapar ett virtuellt Azure-nätverk med två undernät, `ManagedInstance` och `Management`. SQL-hanterad instans kommer att distribueras i `ManagedInstance` undernät. Virtuell dator med den senaste versionen av SQL Server Management Studio (SSMS) kommer att distribueras i undernätet `Management`. |

---
