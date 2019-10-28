---
title: Arkitektur för Azure SQL Database och SQL Data Warehouse anslutning | Microsoft Docs
description: I det här dokumentet förklaras Azure SQL Connectivity-arkitekturen för databas anslutningar från Azure eller utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/02/2019
ms.openlocfilehash: f26eb44dd407e379d0bf3291eb890d2e451c919e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72807927"
---
# <a name="azure-sql-connectivity-architecture"></a>Arkitektur för Azure SQL-anslutning

I den här artikeln beskrivs Azure SQL Database och SQL Data Warehouse anslutnings arkitektur samt hur olika komponenter fungerar för att dirigera trafik till din instans av Azure SQL. Dessa anslutnings komponenter fungerar för att dirigera nätverks trafik till Azure SQL Database eller SQL Data Warehouse med klienter som ansluter inifrån Azure och med klienter som ansluter från utanför Azure. Den här artikeln innehåller också skript exempel för att ändra hur anslutningen sker och de överväganden som rör ändring av inställningarna för standard anslutning.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över Azure SQL Database anslutnings arkitekturen.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Följande steg beskriver hur en anslutning upprättas till en Azure SQL-databas:

- Klienter ansluter till gatewayen, som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på principen gällande anslutning, omdirigerar eller proxyservrarar trafiken till rätt databas kluster.
- I databas klustret vidarebefordras trafiken till rätt Azure SQL-databas.

## <a name="connection-policy"></a>Anslutnings princip

Azure SQL Database stöder följande tre alternativ för anslutnings princip inställningen för en SQL Database-Server:

- **Omdirigera (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen. För att möjliggöra anslutning måste klienterna tillåta utgående brand Väggs regler till alla Azure IP-adresser i regionen med hjälp av nätverks säkerhets grupper (NSG) med [service märken](../virtual-network/security-overview.md#service-tags) för portarna 11000-11999, inte bara Azure SQL Database Gateway-IP-adresserna på port 1433. Eftersom paket går direkt till databasen har svars tiden och data flödet bättre prestanda.
- **Proxy:** I det här läget är alla anslutningar via proxy via Azure SQL Database gatewayer. För att aktivera anslutning måste klienten ha utgående brand Väggs regler som endast tillåter IP-adresser för Azure SQL Database Gateway (vanligt vis två IP-adresser per region). Att välja det här läget kan resultera i högre latens och lägre data flöde, beroende på arbets Belastningens natur. Vi rekommenderar starkt `Redirect` anslutnings princip via `Proxy` anslutnings princip för lägsta latens och högsta data flöde.
- **Standard:** Detta är anslutnings principen som används på alla servrar när den har skapats, såvida du inte uttryckligen ändrar anslutnings principen till antingen `Proxy` eller `Redirect`. Den effektiva principen beror på om anslutningar härstammar från Azure (`Redirect`) eller utanför Azure (`Proxy`).

## <a name="connectivity-from-within-azure"></a>Anslutning inifrån Azure

Om du ansluter inifrån Azure får anslutningarna `Redirect` som standard. En princip för `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL Database omdirigeras sedan klientsessionen till rätt databas kluster med en ändring i den virtuella mål-IP-adressen från den Azure SQL Database gatewayen till den flernodskluster. Därefter flödar alla efterföljande paket direkt till klustret och hoppar över Azure SQL Database Gateway. Följande diagram illustrerar det här trafikflödet.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutning från utanför Azure

Om du ansluter från en plats utanför Azure, har anslutningarna en anslutnings princip för `Proxy` som standard. En princip för `Proxy` innebär att TCP-sessionen upprättas via Azure SQL Database gateway och alla efterföljande paket flöden via gatewayen. Följande diagram illustrerar det här trafikflödet.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

## <a name="azure-sql-database-gateway-ip-addresses"></a>Gateway-IP-adresser för Azure SQL Database

I tabellen nedan visas IP-adresserna för gatewayer per region. Om du vill ansluta till en Azure SQL Database måste du tillåta nätverks trafik att & från **alla** gatewayer i regionen.

Information om hur trafiken ska migreras till nya gateways i vissa regioner finns i följande artikel: [Azure SQL Database trafikmigrering till nyare gateways](sql-database-gateway-migration.md)


| Regionsnamn          | IP-adresser för gateway |
| --- | --- |
| Australien, centrala    | 20.36.105.0 |
| Australien, Central2   | 20.36.113.0 |
| Östra Australien       | 13.75.149.87, 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109, 13.73.109.251 |
| Södra Brasilien         | 104.41.11.5, 191.233.200.14 |
| Kanada, centrala       | 40.85.224.249      |
| Östra Kanada          | 40.86.226.166      |
| Centrala USA           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Kina, östra           | 139.219.130.35     |
| Kina, östra 2         | 40.73.82.1         |
| Kina, norra          | 139.219.15.17      |
| Kina, norra 2        | 40.73.50.0         |
| Östasien            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA, östra              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA, östra 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Frankrike, centrala       | 40.79.137.0, 40.79.129.1 |
| Centrala Tyskland      | 51.4.144.100       |
| Tyskland, norra öst   | 51.5.144.179       |
| Centrala Indien        | 104.211.96.159     |
| Södra Indien          | 104.211.224.146    |
| Västra Indien           | 104.211.160.80     |
| Japan, östra           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Västra Japan           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Sydkorea, centrala        | 52.231.32.42       |
| Sydkorea, södra          | 52.231.200.86      |
| USA, norra centrala     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Norra Europa         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Sydafrika, norra   | 102.133.152.0      |
| Sydafrika, västra    | 102.133.24.0       |
| Södra centrala USA     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Sydostasien      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Förenade Arabemiraten, centrala          | 20.37.72.64        |
| Förenade Arabemiraten, norra            | 65.52.248.0        |
| Storbritannien, södra             | 51.140.184.11      |
| Storbritannien, västra              | 51.141.8.11        |
| USA, västra centrala      | 13.78.145.25       |
| Europa, västra          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA, västra              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| Västra USA 2            | 13.66.226.202      |
|                      |                    |

## <a name="change-azure-sql-database-connection-policy"></a>Ändra Azure SQL Database anslutnings princip

Om du vill ändra Azure SQL Database anslutnings princip för en Azure SQL Database-Server använder [du kommandot](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) för att ansluta.

- Om din anslutnings princip är inställd på `Proxy`, flödar alla nätverks paket via Azure SQL Database Gateway. För den här inställningen behöver du bara tillåta utgående till Azure SQL Database Gateway-IP. Att använda en inställning av `Proxy` har mer latens än en inställning av `Redirect`.
- Om din anslutnings princip anger `Redirect`, flödar alla nätverks paket direkt till databas klustret. För den här inställningen måste du tillåta utgående till flera IP-adresser.

## <a name="script-to-change-connection-settings-via-powershell"></a>Skript för att ändra anslutnings inställningar via PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar anslutnings principen.

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>Skript för att ändra anslutnings inställningar via Azure CLI

> [!IMPORTANT]
> Det här skriptet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

> [!IMPORTANT]
> Det här skriptet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Följande CLI-skript visar hur du ändrar anslutnings principen i ett bash-gränssnitt.

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI från en kommando tolk i Windows

> [!IMPORTANT]
> Det här skriptet kräver [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Följande CLI-skript visar hur du ändrar anslutnings principen från en kommando tolk i Windows (med Azure CLI installerat).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar Azure SQL Database anslutnings princip för en Azure SQL Database-Server finns i avsnittet om att ansluta [-princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om hur du Azure SQL Database anslutnings beteende för klienter som använder ADO.NET 4,5 eller en senare version finns i [portar bortom 1433 för ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän översikts information om program utveckling finns i [Översikt över SQL Database program utveckling](sql-database-develop-overview.md).
