---
title: Anslutningsarkitektur - Azure Database för PostgreSQL - Single Server
description: Beskriver anslutningsarkitekturen för din Azure-databas för PostgreSQL - Single Server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: b0af19ec740e96cd572ffe37285d7e58ead83b25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774849"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Anslutningsarkitektur i Azure Database för PostgreSQL
I den här artikeln beskrivs Azure Database for PostgreSQL-anslutningsarkitektur samt hur trafiken dirigeras till din Azure-databas för PostgreSQL-databasinstans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Anslutning till din Azure-databas för PostgreSQL upprättas via en gateway som ansvarar för routning av inkommande anslutningar till den fysiska platsen för din server i våra kluster. Följande diagram illustrerar trafikflödet.

![Översikt över anslutningsarkitekturen](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

När klienten ansluter till databasen får de en anslutningssträng som ansluter till gatewayen. Den här gatewayen har en offentlig IP-adress som lyssnar på port 5432. Inuti vidarebefordras databasklustertrafiken till lämplig Azure-databas för PostgreSQL. Därför, för att ansluta till din server, till exempel från företagsnätverk, är det nödvändigt att öppna brandväggen på klientsidan så att utgående trafik kan nå våra gateways. Nedan hittar du en komplett lista över de IP-adresser som används av våra gateways per region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Azure-databas för IP-adresser för PostgreSQL-gateway
I följande tabell visas de primära och sekundära IP-adresser i Azure-databasen för PostgreSQL-gateway för alla dataregioner. Den primära IP-adressen är gatewayens aktuella IP-adress och den andra IP-adressen är en IP-adress för redundans vid fel på den primära. Som nämnts bör kunder tillåta utgående till båda IP-adresserna. Den andra IP-adressen lyssnar inte på några tjänster förrän den aktiveras av Azure Database för PostgreSQL för att acceptera anslutningar.

| **Regionsnamn** | **GATEWAY IP-adresser** |
|:----------------|:-------------|
| Australien, centrala| 20.36.105.0     |
| Australien Central2     | 20.36.113.0   |
| Australien, östra | 13.75.149.87, 40.79.161.1     |
| Sydöstra Australien |191.239.192.109, 13.73.109.251   |
| Brasilien, södra | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Kanada, centrala |40.85.224.249  |
| Kanada, östra | 40.86.226.166    |
| USA, centrala | 23.99.160.139, 13.67.215.62   |
| Kina, östra | 139.219.130.35    |
| Kina Öst 2 | 40.73.82.1  |
| Kina, norra | 139.219.15.17    |
| Kina Norra 2 | 40.73.50.0     |
| Asien, östra | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| USA, östra | 40.121.158.30, 191.238.6.43  |
| USA, östra 2 |40.79.84.180, 191.239.224.107, 52.177.185.181   |
| Frankrike, centrala | 40.79.137.0, 40.79.129.1  |
| Tyskland, centrala | 51.4.144.100     |
| Tyskland Nordöstra | 51.5.144.179  |
| Indien, centrala | 104.211.96.159     |
| Södra Indien | 104.211.224.146  |
| Indien, västra | 104.211.160.80    |
| Japan, östra | 13.78.61.196, 191.237.240.43  |
| Japan, västra | 104.214.148.156, 191.238.68.11, 40.74.96.6, 40.74.96.7    |
| Sydkorea, centrala | 52.231.32.42   |
| Sydkorea, södra | 52.231.200.86    |
| USA, norra centrala | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa, norra | 40.113.93.91, 191.235.193.75    |
| Sydafrika North  | 102.133.152.0    |
| Sydafrika Väst | 102.133.24.0   |
| USA, södra centrala |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0   |
| Sydostasien | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| Centrala Förenade Arabemiraten | 20.37.72.64  |
| Uae Norra | 65.52.248.0    |
| Storbritannien, södra | 51.140.184.11   |
| Storbritannien, västra | 51.141.8.11  |
| USA, västra centrala | 13.78.145.25     |
| Europa, västra | 40.68.37.158, 191.237.232.75     |
| USA, västra | 104.42.238.205, 23.99.34.75  |
| USA, västra 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med hjälp av Azure-portalen](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database för PostgreSQL-brandväggsregler med Hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)
