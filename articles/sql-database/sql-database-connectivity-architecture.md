---
title: Anslutnings arkitektur
description: I det här dokumentet förklaras Azure SQL Connectivity-arkitekturen för databas anslutningar från Azure eller utanför Azure.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 6fdfbce6dce2428a8f2757b0755e6f982f02240f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256424"
---
# <a name="azure-sql-connectivity-architecture"></a>Arkitektur för Azure SQL-anslutning
> [!NOTE]
> Den här artikeln gäller för Azure SQL Server och för både SQL Database och SQL Data Warehouse databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* för **Azure SQL Database Hanterad instans**. Se [anslutnings arkitekturen för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).

Den här artikeln förklarar arkitekturen i olika komponenter som dirigerar nätverks trafik till Azure SQL Database eller SQL Data Warehouse. Den förklarar också olika anslutnings principer och hur de påverkar klienter som ansluter från Azure och klienter som ansluter utanför Azure. 

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt över Azure SQL Database anslutnings arkitekturen.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-overview.png)

Följande steg beskriver hur en anslutning upprättas till en Azure SQL-databas:

- Klienter ansluter till gatewayen, som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på principen gällande anslutning, omdirigerar eller proxyservrarar trafiken till rätt databas kluster.
- I databas klustret vidarebefordras trafiken till rätt Azure SQL-databas.

## <a name="connection-policy"></a>Anslutnings princip

Azure SQL Database stöder följande tre alternativ för anslutnings princip inställningen för en SQL Database-Server:

- **Omdirigera (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen, vilket leder till minskad svars tid och bättre data flöde. För att anslutningar ska kunna använda det här läget måste klienterna:
   - Tillåt utgående kommunikation från klienten till alla Azure IP-adresser i regionen på portarna i intervallet 11000 11999. Använd tjänst taggarna för SQL för att göra det enklare att hantera.  
   - Tillåt utgående kommunikation från klienten till Azure SQL Database Gateway-IP-adresser på port 1433.

- **Proxy:** I det här läget är alla anslutningar via proxy via Azure SQL Database gatewayer, vilket leder till ökad latens och minskat data flöde. För att anslutningar ska kunna använda det här läget måste klienterna tillåta utgående kommunikation från klienten till Azure SQL Database Gateway-IP-adresser på port 1433.

- **Standard:** Detta är anslutnings principen som används på alla servrar när den har skapats, såvida du inte uttryckligen ändrar anslutnings principen till antingen `Proxy` eller `Redirect`. Standard principen är`Redirect` för alla klient anslutningar som har sitt ursprung i Azure (till exempel från en virtuell Azure-dator) och `Proxy`för alla klient anslutningar som kommer från utsidan (till exempel anslutningar från din lokala arbets Station).

 Vi rekommenderar starkt `Redirect` anslutnings princip via `Proxy` anslutnings princip för lägsta latens och högsta data flöde. Du måste dock uppfylla de ytterligare kraven för att tillåta nätverks trafik enligt beskrivningen ovan. Om klienten är en virtuell Azure-dator kan du göra detta med hjälp av nätverks säkerhets grupper (NSG) med [service märken](../virtual-network/security-overview.md#service-tags). Om klienten ansluter från en lokal arbets Station kan du behöva arbeta med nätverks administratören för att tillåta nätverks trafik via företags brand väggen.

## <a name="connectivity-from-within-azure"></a>Anslutning inifrån Azure

Om du ansluter inifrån Azure får anslutningarna `Redirect` som standard. En princip för `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL Database omdirigeras sedan klientsessionen till rätt databas kluster med en ändring i den virtuella mål-IP-adressen från den Azure SQL Database gatewayen till klustret. Därefter flödar alla efterföljande paket direkt till klustret och hoppar över Azure SQL Database Gateway. Följande diagram illustrerar det här trafikflödet.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutning från utanför Azure

Om du ansluter från en plats utanför Azure, har anslutningarna en anslutnings princip för `Proxy` som standard. En princip för `Proxy` innebär att TCP-sessionen upprättas via Azure SQL Database gateway och alla efterföljande paket flöden via gatewayen. Följande diagram illustrerar det här trafikflödet.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Öppna även portarna 14000-14999 för att aktivera [anslutning med DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Gateway-IP-adresser för Azure SQL Database

I tabellen nedan visas IP-adresserna för gatewayer per region. Om du vill ansluta till en Azure SQL Database måste du tillåta nätverks trafik att & från **alla** gatewayer i regionen.

Information om hur trafiken ska migreras till nya gateways i vissa regioner finns i följande artikel: [Azure SQL Database trafikmigrering till nyare gateways](sql-database-gateway-migration.md)


| Regionsnamn          | IP-adresser för gateway |
| --- | --- |
| Australien, centrala    | 20.36.105.0 |
| Australien, Central2   | 20.36.113.0 |
| Australien, östra       | 13.75.149.87, 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109, 13.73.109.251 |
| Brasilien, södra         | 104.41.11.5, 191.233.200.14 |
| Kanada, centrala       | 40.85.224.249      |
| Kanada, östra          | 40.86.226.166      |
| USA, centrala           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Kina, östra           | 139.219.130.35     |
| Kina, östra 2         | 40.73.82.1         |
| Kina, norra          | 139.219.15.17      |
| Kina, norra 2        | 40.73.50.0         |
| Asien, östra            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA, östra              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA, östra 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Frankrike, centrala       | 40.79.137.0, 40.79.129.1 |
| Tyskland, centrala      | 51.4.144.100       |
| Tyskland, norra öst   | 51.5.144.179       |
| Indien, centrala        | 104.211.96.159     |
| Södra Indien          | 104.211.224.146    |
| Indien, västra           | 104.211.160.80     |
| Japan, östra           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japan, västra           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Sydkorea, centrala        | 52.231.32.42       |
| Sydkorea, södra          | 52.231.200.86      |
| USA, norra centrala     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa, norra         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Östra Norge          | 51.120.96.0        |
| Norge, väst          | 51.120.216.0       |
| Sydafrika, norra   | 102.133.152.0      |
| Sydafrika, västra    | 102.133.24.0       |
| USA, södra centrala     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Sydostasien      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Förenade Arabemiraten Central          | 20.37.72.64        |
| Förenade Arabemiraten, norra            | 65.52.248.0        |
| Storbritannien, södra             | 51.140.184.11      |
| Storbritannien, västra              | 51.141.8.11        |
| USA, västra centrala      | 13.78.145.25       |
| Europa, västra          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA, västra              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA, västra 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar Azure SQL Database anslutnings princip för en Azure SQL Database-Server finns i avsnittet om att ansluta [-princip](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om hur du Azure SQL Database anslutnings beteende för klienter som använder ADO.NET 4,5 eller en senare version finns i [portar bortom 1433 för ADO.NET 4,5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän översikts information om program utveckling finns i [Översikt över SQL Database program utveckling](sql-database-develop-overview.md).
