---
title: Meddelande för gateway-migrering för Azure SQL Database från Gen2 till Gen3 | Microsoft Docs
description: Artikeln innehåller meddelande till användare om migreringen av Azure SQL Database-gatewayer IP-adresser
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
manager: craigg
ms.date: 07/01/2019
ms.openlocfilehash: 5894579c62c524394c7fea044b96885f7c8e8204
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538385"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafik migrering till nyare Gateways

Eftersom Azure-infrastrukturen förbättrar uppdaterar regelbundet Microsoft maskinvara för att se till att vi ger bästa möjliga kundupplevelse. Under de kommande månaderna kommer vi planerar att lägga till Gateways som bygger på nyare maskinvarugenerationer och inaktivera gatewayer som bygger på äldre maskinvara i vissa regioner.  

Kunder kommer att meddelas via e-post och i Azure-portalen långt innan ändringar gatewayer som är tillgängliga i varje region. Den senaste informationen kommer att finnas kvar i den [IP-adresser för Azure SQL Database-gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) tabell.

## <a name="impact-of-this-change"></a>Effekten av ändringen

Den första runda över inaktivering av Gateway har schemalagts för den 1 September 2019 i följande regioner:

- Västra USA
- Västra Europa
- East US
- Centrala USA
- Sydostasien
- Södra centrala USA
- Norra Europa
- Norra centrala USA
- Västra Japan
- Östra Japan
- USA, östra 2
- Östasien

IP-adressen ur slutar att acceptera trafik och eventuella nya anslutningsförsök kommer att dirigeras till en gateway i regionen.

Där du ser inte effekten av ändringen:

- Kunder som använder omdirigering som sin anslutningsprincip inte kommer se någon inverkan.
- Anslutningar till SQL Database från inuti Azure och använder Tjänsttaggar påverkas inte.
- Anslutningar med versioner som stöds av JDBC-drivrutinen för SQL Server ser påverkas inte. JDBC-versioner som stöds, se [ladda ned Microsoft JDBC-drivrutinen för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad kan jag göra om påverkas du

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla de [IP-adresser för Azure SQL Database-gateway](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) i regionen på TCP-port 1433 och port intervallet 11000-11999 i enheten för brandväggen. Läs mer på portintervall [anslutningsprincip](sql-database-connectivity-architecture.md#connection-policy).

Anslutningar från program som använder Microsoft JDBC-drivrutinen nedan version 4.0 misslyckas certifikatsverifiering. Lägre versioner av Microsoft JDBC förlitar sig på nätverksnamn (CN) i ämnesfältet för certifikatet. Minskningen är att säkerställa att egenskapen hostNameInCertificate anges till *. database.windows.net. Läs mer om hur du anger egenskapen hostNameInCertificate [ansluter med SSL-kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående minskningen inte fungerar kan du skicka en supportbegäran om för SQL-databas som använder följande URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [arkitektur för Azure SQL-anslutning](sql-database-connectivity-architecture.md)