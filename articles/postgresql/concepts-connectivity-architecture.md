---
title: Anslutningsarkitektur i Azure Database för PostgreSQL
description: Beskriver arkitekturen anslutning för din Azure Database for PostgreSQL-server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 0d91458c555c819c4bcf97215a712719ebc5eb71
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588947"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Anslutningsarkitektur i Azure Database för PostgreSQL
Den här artikeln förklarar Azure Database for PostgreSQL anslutningsarkitektur samt hur trafiken dirigeras till din Azure Database for PostgreSQL database-instans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Via en gateway som ansvarar för inkommande anslutningar till den fysiska platsen för din server i våra upprättas anslutningen till din Azure Database för PostgreSQL. Följande diagram illustrerar trafikflödet.

![Översikt över arkitekturen för anslutning](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Som klient ansluta till databasen, får de en anslutningssträng som ansluter till gatewayen. Denna gateway har en offentlig IP-adress som lyssnar på port 5432. Inne i databasen vidarebefordras clusterz trafik till lämplig Azure Database för PostgreSQL. Därför för att ansluta till din server, till exempel från företagets nätverk, är det nödvändigt att öppna klienten sida brandväggen att tillåta utgående trafik för att kunna nå våra gatewayer. Nedan hittar du en fullständig lista över IP-adresser som används av våra gatewayer per region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure Database för PostgreSQL gateway IP-adresser
I följande tabell visas de primära och sekundära IP-adresserna för Azure Database for PostgreSQL-gateway för alla dataområden. Den primära IP-adressen är den aktuella IP-adressen till gatewayen och den andra IP-adressen är en IP-adress för redundans vid avbrott för primärt. Som tidigare nämnts kan ska kunder tillåta utgående trafik till båda IP-adresserna. Den andra IP-adressen lyssnar inte på alla tjänster förrän den aktiveras av Azure Database för PostgreSQL att acceptera anslutningar.

| **Regionsnamn** | **Primär IP-adress** | **Sekundär IP-adress** |
|:----------------|:-------------|:------------------------|
| Östra Australien | 13.75.149.87 | 40.79.161.1 |
| Sydöstra Australien | 191.239.192.109 | 13.73.109.251 |
| Södra Brasilien | 104.41.11.5 | |
| Centrala Kanada | 40.85.224.249 | |
| Östra Kanada | 40.86.226.166 | |
| Centrala USA | 23.99.160.139 | 13.67.215.62 |
| Östra Kina 1 | 139.219.130.35 | |
| Kina, östra 2 | 40.73.82.1 | |
| Norra Kina 1 | 139.219.15.17 | |
| Kina, norra 2 | 40.73.50.0 | |
| Östasien | 191.234.2.139 | 52.175.33.150 |
| Östra USA 1 | 191.238.6.43 | 40.121.158.30 |
| USA, östra 2 | 191.239.224.107 | 40.79.84.180 * |
| Frankrike, centrala | 40.79.137.0 | 40.79.129.1 |
| Centrala Tyskland | 51.4.144.100 | |
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
| Västra USA 1 | 23.99.34.75 | 104.42.238.205 |
| Västra USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Östra USA 2* har också en tertiär IP-adressen för `52.167.104.0`.

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure-databas för PostgreSQL brandväggsregler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
