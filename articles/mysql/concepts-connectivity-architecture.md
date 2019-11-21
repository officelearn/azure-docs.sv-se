---
title: Connectivity architecture in Azure Database for MySQL
description: Describes the connectivity architecture for your Azure Database for MySQL server.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: c4fecfadefedf10f7e11534b4efbd197c4d7fdae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74213140"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Connectivity architecture in Azure Database for MySQL
This article explains the Azure Database for MySQL connectivity architecture as well as how the traffic is directed to your Azure Database for MySQL instance from clients both within and outside Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Connection to your Azure Database for MySQL is established through a gateway that is responsible for routing incoming connections to the physical location of your server in our clusters. The following diagram illustrates the traffic flow.

![Overview of the connectivity architecture](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

As client connect to the database, they get a connection string which connects to the gateway. This gateway has a public IP address that listens to port 3306. Inside the database cluster, traffic is forwarded to appropriate Azure Database for MySQL. Therefore, in order to connect to your server, such as from corporate networks, it is necessary to open up the client side firewall to allow outbound traffic to be able to reach our gateways. Below you can find a complete list of the IP addresses used by our gateways per region.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL gateway IP addresses
The following table lists the primary and secondary IPs of the Azure Database for MySQL gateway for all data regions. The primary IP address is the current IP address of the gateway and the second IP address is a failover IP address in case of failure of the primary. As mentioned, customers should allow outbound to both the IP addresses. The second IP address does not listen in on any services until it is activated by Azure Database for MySQL to accept connections.

| **Region Name** | **Primary IP Address** | **Secondary IP Address** |
|:----------------|:-------------|:------------------------|
| Australien, östra | 13.75.149.87 | 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Brasilien, södra | 104.41.11.5 | |
| Kanada, centrala | 40.85.224.249 | |
| Kanada, östra | 40.86.226.166 | |
| USA, centrala | 23.99.160.139 | 13.67.215.62 |
| China East 1 | 139.219.130.35 | |
| Kina, östra 2 | 40.73.82.1 | |
| China North 1 | 139.219.15.17 | |
| Kina, norra 2 | 40.73.50.0 | |
| Asien, östra | 191.234.2.139 | 52.175.33.150 |
| East US 1 | 191.238.6.43 | 40.121.158.30 |
| USA, östra 2 | 191.239.224.107 | 40.79.84.180 * |
| Frankrike, centrala | 40.79.137.0 | 40.79.129.1 |
| Tyskland, centrala | 51.4.144.100 | |
| Centrala Indien | 104.211.96.159 | |
| Södra Indien | 104.211.224.146 | |
| Västra Indien | 104.211.160.80 | |
| Japan, östra | 191.237.240.43 | 13.78.61.196 |
| Japan, västra | 191.238.68.11 | 104.214.148.156 |
| Sydkorea, centrala | 52.231.32.42 | |
| Sydkorea, södra | 52.231.200.86 |  |
| USA, norra centrala | 23.98.55.75 | 23.96.178.199 |
| Europa, norra | 191.235.193.75 | 40.113.93.91 |
| USA, södra centrala | 23.98.162.75 | 13.66.62.124 |
| Sydostasien | 23.100.117.95 | 104.43.15.0 |
| Sydafrika, norra | 102.133.152.0 | |
| Sydafrika, västra | 102.133.24.0 | |
| Förenade Arabemiraten, norra | 65.52.248.0 | |
| Storbritannien, södra | 51.140.184.11 | |
| Storbritannien, västra | 51.141.8.11| |
| Europa, västra | 191.237.232.75 | 40.68.37.158 |
| West US 1 | 23.99.34.75 | 104.42.238.205 |
| USA, västra 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *East US 2* has also a tertiary IP address of `52.167.104.0`.

## <a name="connection-redirection"></a>Connection redirection

Azure Database for MySQL supports an additional connection policy, **redirection**, that helps to reduce network latency between client applications and MySQL servers. With this feature, after the initial TCP session is established to the Azure Database for MySQL server, the server returns the backend address of the node hosting the MySQL server to the client. Thereafter, all subsequent packets flow directly to the server, bypassing the gateway. As packets flow directly to the server, latency and throughput have improved performance.

This feature is supported in Azure Database for MySQL servers with engine versions 5.6, 5.7, and 8.0.

Preview support for redirection is available in the [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extension, developed by Microsoft, and is available on [PECL](https://pecl.php.net/package/mysqlnd_azure). See the [configuring redirection](./howto-redirection.md) article for more information on how to use redirection in your applications. 

## <a name="next-steps"></a>Nästa steg

* [Create and manage Azure Database for MySQL firewall rules using the Azure portal](./howto-manage-firewall-using-portal.md)
* [Create and manage Azure Database for MySQL firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)