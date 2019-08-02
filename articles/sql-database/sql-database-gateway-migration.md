---
title: Meddelande om Gateway-migrering för Azure SQL Database från Gen2 till Gen3 | Microsoft Docs
description: Artikeln innehåller information om migrering av Azure SQL Database Gateway-IP-adresser
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 85691464684ff327c01a85bf357514f447564dd7
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568117"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways

När Azure-infrastrukturen förbättras kommer Microsoft regelbundet att uppdatera maskin varan så att vi ger bästa möjliga kund upplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som bygger på nyare maskin varu generationer och avställnings bara gatewayer som bygger på äldre maskin vara i vissa regioner.  

Kunderna får ett meddelande via e-post och i Azure Portal väl i förväg om eventuella ändringar av gatewayer som är tillgängliga i varje region. Den senaste informationen kommer att behållas i tabellen [Azure SQL Database Gateway IP-adresser](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Effekt av den här ändringen

Den första rundan av Gateway-avställningen är schemalagd för den 1 september 2019 i följande regioner:

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

Den inaktiverade IP-adressen slutar att acceptera trafik och eventuella nya anslutnings försök dirigeras till en av Gatewayerna i regionen.

Där du inte ser effekten av den här ändringen:

- Kunder som använder omdirigering som anslutnings princip ser inte någon påverkan.
- Anslutningar till SQL Database inifrån Azure och användningen av service märken påverkas inte.
- Anslutningar som görs med stödda versioner av JDBC-drivrutinen för SQL Server kommer inte att se någon påverkan. Information om vilka JDBC-versioner som stöds finns i [Hämta Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du gör om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [Azure SQL Database Gateway-IP-adresser](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) i regionen på TCP-port 1433 och port intervall 11000-11999 i brand Väggs enheten. Mer information om Port intervall finns i [anslutnings princip](sql-database-connectivity-architecture.md#connection-policy).

Anslutningar som görs från program som använder Microsoft JDBC driv rutinen under version 4,0 kan inte verifiera certifikat verifiering. Lägre versioner av Microsoft JDBC förlitar sig på eget namn (CN) i certifikatets ämnes fält. Minskningen är att se till att egenskapen hostNameInCertificate är inställd på *. database.windows.net. Mer information om hur du anger egenskapen hostNameInCertificate finns i [ansluta med SSL-kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående minskning inte fungerar kan du skicka en supportbegäran för SQL Database med hjälp av följande URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure SQL Connectivity-arkitekturen](sql-database-connectivity-architecture.md)