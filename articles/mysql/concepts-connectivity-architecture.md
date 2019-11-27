---
title: Anslutnings arkitektur i Azure Database for MySQL
description: Beskriver anslutnings arkitekturen för din Azure Database for MySQL-server.
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
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Anslutnings arkitektur i Azure Database for MySQL
Den här artikeln förklarar Azure Database for MySQL anslutnings arkitektur och hur trafiken dirigeras till din Azure Database for MySQL-instans från klienter både inom och utanför Azure.

## <a name="connectivity-architecture"></a>Anslutningsarkitektur
Anslutning till din Azure Database for MySQL upprättas via en gateway som ansvarar för att dirigera inkommande anslutningar till serverns fysiska plats i våra kluster. Följande diagram illustrerar trafikflödet.

![Översikt över anslutnings arkitekturen](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

När klienten ansluter till databasen får de en anslutnings sträng som ansluter till gatewayen. Den här gatewayen har en offentlig IP-adress som lyssnar på port 3306. I databas klustret vidarebefordras trafiken till lämpliga Azure Database for MySQL. För att kunna ansluta till servern, t. ex. från företags nätverk, är det därför nödvändigt att öppna brand väggen på klient sidan så att utgående trafik kan komma åt våra gateways. Nedan hittar du en fullständig lista över de IP-adresser som används av våra gatewayer per region.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database for MySQL Gateway-IP-adresser
I följande tabell visas de primära och sekundära IP-adresserna för Azure Database for MySQL Gateway för alla data områden. Den primära IP-adressen är den aktuella IP-adressen för gatewayen och den andra IP-adressen är en IP-adress för redundans vid fel i den primära. Som nämnts bör kunderna tillåta utgående till båda IP-adresserna. Den andra IP-adressen lyssnar inte på några tjänster förrän den har Aktiver ATS av Azure Database for MySQL att acceptera anslutningar.

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
| Sydafrika, norra | 102.133.152.0 | |
| Sydafrika, västra | 102.133.24.0 | |
| Förenade Arabemiraten, norra | 65.52.248.0 | |
| Storbritannien, södra | 51.140.184.11 | |
| Storbritannien, västra | 51.141.8.11| |
| Västra Europa | 191.237.232.75 | 40.68.37.158 |
| USA, västra 1 | 23.99.34.75 | 104.42.238.205 |
| Västra USA 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *Östra USA 2* har även en tertiär IP-adress för `52.167.104.0`.

## <a name="connection-redirection"></a>Omdirigering av anslutning

Azure Database for MySQL stöder ytterligare en anslutnings princip, **omdirigering**som hjälper till att minska nätverks fördröjningen mellan klient program och MySQL-servrar. Med den här funktionen returnerar servern den nod som är värd för MySQL-servern till klienten när den första TCP-sessionen har upprättats till Azure Database for MySQL servern. Därefter flödar alla efterföljande paket direkt till servern, vilket kringgår gatewayen. När paket flödar direkt till servern har svars tiden och data flödet bättre prestanda.

Den här funktionen stöds i Azure Database for MySQL servrar med motor versionerna 5,6, 5,7 och 8,0.

För hands versions stöd för omdirigering är tillgängligt i [PHP mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -tillägget, som har utvecklats av Microsoft och finns på [PECL](https://pecl.php.net/package/mysqlnd_azure). I artikeln [Konfigurera omdirigering](./howto-redirection.md) finns mer information om hur du använder omdirigering i dina program. 

## <a name="next-steps"></a>Nästa steg

* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure Portal](./howto-manage-firewall-using-portal.md)
* [Skapa och hantera Azure Database for MySQL brand Väggs regler med hjälp av Azure CLI](./howto-manage-firewall-using-cli.md)