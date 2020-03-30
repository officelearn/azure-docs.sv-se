---
title: Anslutningsarkitektur
description: Det här dokumentet förklarar Azure SQL-anslutningsarkitekturen för databasanslutningar från Azure eller utanför Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256424"
---
# <a name="azure-sql-connectivity-architecture"></a>Anslutningsarkitektur för Azure SQL
> [!NOTE]
> Den här artikeln gäller Azure SQL-server och både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

> [!IMPORTANT]
> Den här artikeln gäller *inte* **för Azure SQL Database Managed Instance**. Se [anslutningsarkitektur för en hanterad instans](sql-database-managed-instance-connectivity-architecture.md).

I den här artikeln beskrivs arkitekturen för olika komponenter som dirigerar nätverkstrafik till Azure SQL Database eller SQL Data Warehouse. Det förklarar också olika anslutningsprinciper och hur det påverkar klienter som ansluter från Azure och klienter som ansluter från utanför Azure. 

## <a name="connectivity-architecture"></a>Anslutningsarkitektur

Följande diagram ger en översikt på hög nivå över Azure SQL Database-anslutningsarkitekturen.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-overview.png)

I följande steg beskrivs hur en anslutning upprättas till en Azure SQL-databas:

- Klienter ansluter till gatewayen, som har en offentlig IP-adress och lyssnar på port 1433.
- Gatewayen, beroende på den gällande anslutningsprincipen, omdirigerar eller proxyservrar trafiken till rätt databaskluster.
- Inuti vidarebefordras databasklustertrafiken till lämplig Azure SQL-databas.

## <a name="connection-policy"></a>Anslutningsprincip

Azure SQL Database stöder följande tre alternativ för anslutningsprincipen för en SQL Database-server:

- **Omdirigering (rekommenderas):** Klienter upprättar anslutningar direkt till noden som är värd för databasen, vilket leder till minskad latens och förbättrat dataflöde. För att anslutningar ska kunna använda det här läget måste klienter:
   - Tillåt utgående kommunikation från klienten till alla Azure IP-adresser i regionen på portar i intervallet 11000 11999. Använd tjänsttaggarna för SQL för att göra det enklare att hantera.  
   - Tillåt utgående kommunikation från klienten till AZURE SQL Database gateway IP-adresser på port 1433.

- **Proxy:** I det här läget proxied alla anslutningar via Azure SQL Database gateways, vilket leder till ökad svarstid och minskat dataflöde. För att anslutningar ska kunna använda det här läget måste klienter tillåta utgående kommunikation från klienten till AZURE SQL Database gateway IP-adresser på port 1433.

- **Standard:** Detta är anslutningsprincipen som gäller på alla servrar efter att ha `Proxy` `Redirect`skapats om du inte uttryckligen ändrar anslutningsprincipen till antingen eller . Standardprincipen`Redirect` gäller för alla klientanslutningar som kommer in i Azure (till exempel från en virtuell Azure-dator) och `Proxy`för alla klientanslutningar som kommer utanför (till exempel anslutningar från din lokala arbetsstation).

 Vi rekommenderar `Redirect` starkt anslutningsprincipen `Proxy` över anslutningsprincipen för den lägsta svarstiden och det högsta dataflödet. Du måste dock uppfylla de ytterligare kraven för att tillåta nätverkstrafik enligt beskrivningen ovan. Om klienten är en virtuell Azure-dator kan du utföra detta med hjälp av NSG (Network Security Groups) med [tjänsttaggar](../virtual-network/security-overview.md#service-tags). Om klienten ansluter från en lokal arbetsstation kan du behöva arbeta med nätverksadministratören för att tillåta nätverkstrafik via företagets brandvägg.

## <a name="connectivity-from-within-azure"></a>Anslutning från Azure

Om du ansluter inifrån Azure har dina anslutningar `Redirect` en anslutningsprincip som standard. En princip `Redirect` innebär att när TCP-sessionen har upprättats till Azure SQL-databasen omdirigeras klientsessionen sedan till rätt databaskluster med en ändring av målviros ip-adressen från azure SQL Database-gatewayen till klustrets. Därefter flödar alla efterföljande paket direkt till klustret och kringgår Azure SQL Database-gatewayen. Följande diagram illustrerar detta trafikflöde.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Anslutning utanför Azure

Om du ansluter från utanför Azure har dina anslutningar `Proxy` en anslutningsprincip som standard. En princip `Proxy` innebär att TCP-sessionen upprättas via Azure SQL Database-gatewayen och alla efterföljande paket flödar via gatewayen. Följande diagram illustrerar detta trafikflöde.

![arkitektur översikt](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Dessutom öppna portar 14000-14999 för att aktivera [Anslutning med DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>Gateway-IP-adresser för Azure SQL Database

I tabellen nedan visas IP-adresserna för gateways efter region. Om du vill ansluta till en Azure SQL-databas måste du tillåta nätverkstrafik att & från **alla** gateways för regionen.

Information om hur trafiken ska migreras till nya gateways i specifika regioner finns i följande artikel: [Azure SQL Database traffic migration to newer Gateways](sql-database-gateway-migration.md)


| Regionsnamn          | GATEWAY IP-adresser |
| --- | --- |
| Australien, centrala    | 20.36.105.0 |
| Australien Central2   | 20.36.113.0 |
| Australien, östra       | 13.75.149.87, 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109, 13.73.109.251 |
| Brasilien, södra         | 104.41.11.5, 191.233.200.14 |
| Kanada, centrala       | 40.85.224.249      |
| Kanada, östra          | 40.86.226.166      |
| USA, centrala           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| Kina, östra           | 139.219.130.35     |
| Kina Öst 2         | 40.73.82.1         |
| Kina, norra          | 139.219.15.17      |
| Kina Norra 2        | 40.73.50.0         |
| Asien, östra            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| USA, östra              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| USA, östra 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Frankrike, centrala       | 40.79.137.0, 40.79.129.1 |
| Tyskland, centrala      | 51.4.144.100       |
| Tyskland Nordöstra   | 51.5.144.179       |
| Indien, centrala        | 104.211.96.159     |
| Södra Indien          | 104.211.224.146    |
| Indien, västra           | 104.211.160.80     |
| Japan, östra           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japan, västra           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Sydkorea, centrala        | 52.231.32.42       |
| Sydkorea, södra          | 52.231.200.86      |
| USA, norra centrala     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa, norra         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Norge Öst          | 51.120.96.0        |
| Norge Väst          | 51.120.216.0       |
| Sydafrika North   | 102.133.152.0      |
| Sydafrika Väst    | 102.133.24.0       |
| USA, södra centrala     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Sydostasien      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| Centrala Förenade Arabemiraten          | 20.37.72.64        |
| Uae Norra            | 65.52.248.0        |
| Storbritannien, södra             | 51.140.184.11      |
| Storbritannien, västra              | 51.141.8.11        |
| USA, västra centrala      | 13.78.145.25       |
| Europa, västra          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| USA, västra              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| USA, västra 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Nästa steg

- Information om hur du ändrar azure SQL Database-anslutningsprincipen för en Azure SQL Database-server finns i [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Information om Azure SQL Database-anslutningsbeteende för klienter som använder ADO.NET 4.5 eller en senare version finns [i Portar över 1433 för ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).
- Allmän översiktsinformation för programutveckling finns i [ÖVERSIKT ÖVER UTVECKLING AV SQL Database-program](sql-database-develop-overview.md).
