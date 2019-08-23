---
title: Anslutnings arkitektur i Azure Database for PostgreSQL
description: Beskriver anslutnings arkitekturen för din Azure Database for PostgreSQL-Server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 92844f0fe3a851802836015a1340983eb4633ed2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900555"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Anslutnings arkitektur i Azure Database for PostgreSQL
Den här artikeln förklarar Azure Database for PostgreSQL anslutnings arkitektur och hur trafiken dirigeras till din Azure Database for PostgreSQL databas instans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Anslutning till din Azure Database for PostgreSQL upprättas via en gateway som ansvarar för att dirigera inkommande anslutningar till serverns fysiska plats i våra kluster. Följande diagram illustrerar trafikflödet.

![Översikt över anslutnings arkitekturen](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

När klienten ansluter till databasen får de en anslutnings sträng som ansluter till gatewayen. Den här gatewayen har en offentlig IP-adress som lyssnar på port 5432. I databas klustret vidarebefordras trafiken till lämplig Azure Database for PostgreSQL. För att kunna ansluta till servern, t. ex. från företags nätverk, är det därför nödvändigt att öppna brand väggen på klient sidan så att utgående trafik kan komma åt våra gateways. Nedan hittar du en fullständig lista över de IP-adresser som används av våra gatewayer per region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database for PostgreSQL Gateway-IP-adresser
I följande tabell visas de primära och sekundära IP-adresserna för Azure Database for PostgreSQL Gateway för alla data områden. Den primära IP-adressen är den aktuella IP-adressen för gatewayen och den andra IP-adressen är en IP-adress för redundans vid fel i den primära. Som nämnts bör kunderna tillåta utgående till båda IP-adresserna. Den andra IP-adressen lyssnar inte på några tjänster förrän den har Aktiver ATS av Azure Database for PostgreSQL att acceptera anslutningar.

| **Region namn** | **Primär IP-adress** | **Sekundär IP-adress** |
|:----------------|:-------------|:------------------------|
| Östra Australien | 13.75.149.87 | 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Södra Brasilien | 104.41.11.5 | |
| Centrala Kanada | 40.85.224.249 | |
| Östra Kanada | 40.86.226.166 | |
| Centrala USA | 23.99.160.139 | 13.67.215.62 |
| Kina, östra 1 | 139.219.130.35 | |
| Kina, östra 2 | 40.73.82.1 | |
| Kina, norra 1 | 139.219.15.17 | |
| Kina, norra 2 | 40.73.50.0 | |
| Östasien | 191.234.2.139 | 52.175.33.150 |
| USA, östra 1 | 191.238.6.43 | 40.121.158.30 |
| USA, östra 2 | 191.239.224.107 | 40.79.84.180 * |
| Frankrike, centrala | 40.79.137.0 | 40.79.129.1 |
| Tyskland, centrala | 51.4.144.100 | |
| Centrala Indien | 104.211.96.159 | |
| Södra Indien | 104.211.224.146 | |
| Västra Indien | 104.211.160.80 | |
| Östra Japan | 191.237.240.43 | 13.78.61.196 |
| Västra Japan | 191.238.68.11 | 104.214.148.156 |
| Sydkorea, centrala | 52.231.32.42 | |
| Sydkorea, södra | 52.231.200.86 |  |
| Norra centrala USA | 23.98.55.75 | 23.96.178.199 |
| Norra Europa | 191.235.193.75 | 40.113.93.91 |
| Södra centrala USA | 23.98.162.75 | 13.66.62.124 |
| Sydostasien | 23.100.117.95 | 104.43.15.0 |
| Storbritannien, södra | 51.140.184.11 | |
| Storbritannien, västra | 51.141.8.11| |
| Västra Europa | 191.237.232.75 | 40.68.37.158 |
| USA, västra 1 | 23.99.34.75 | 104.42.238.205 |
| Västra USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Östra USA 2* har även en tertiär IP-adress `52.167.104.0`för.

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
