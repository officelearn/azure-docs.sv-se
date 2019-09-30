---
title: Meddelande om migrering av Gateway-trafik för Azure SQL Database | Microsoft Docs
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
ms.openlocfilehash: a2064e0012abea237c1b4c0c6f7e35119c1aeff5
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686809"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways

När Azure-infrastrukturen förbättras kommer Microsoft regelbundet att uppdatera maskin varan så att vi ger bästa möjliga kund upplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som skapats på nyare maskin varu generationer, migrera trafik till dem och sedan göra inprovisionade gatewayer byggda på äldre maskin vara i vissa regioner.  

Kunderna får ett meddelande via e-post och i Azure Portal väl i förväg om eventuella ändringar av gatewayer som är tillgängliga i varje region. Den senaste informationen kommer att behållas i tabellen [Azure SQL Database Gateway IP-adresser](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Effekt av den här ändringen

Den första avrundingen av trafikmigrering till nyare gateways har schemalagts för den **14 oktober 2019** i följande regioner:
- Södra Brasilien
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

Trafikmigreringen kommer att ändra den offentliga IP-adress som DNS matchar för SQL Database.
Du kommer att påverkas om du har:
- Hårdkoda IP-adressen för en viss gateway i din lokala brand vägg
- Undernät som använder Microsoft. SQL som en tjänst slut punkt men inte kan kommunicera med IP-adresser för gateway

Du påverkas inte om du har:
- Omdirigering som anslutnings princip
- Anslutningar till SQL Database inifrån Azure och med service märken
- Anslutningar som görs med stödda versioner av JDBC-drivrutinen för SQL Server kommer inte att se någon påverkan. Information om vilka JDBC-versioner som stöds finns i [Hämta Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du gör om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [Azure SQL Database Gateway-IP-adresser](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) i regionen på TCP-port 1433 och port intervall 11000-11999. Den här rekommendationen gäller för klienter som ansluter lokalt och som också ansluter via tjänst slut punkter. Mer information om Port intervall finns i [anslutnings princip](sql-database-connectivity-architecture.md#connection-policy).

Anslutningar som görs från program som använder Microsoft JDBC driv rutinen under version 4,0 kan inte verifiera certifikat verifiering. Lägre versioner av Microsoft JDBC förlitar sig på eget namn (CN) i certifikatets ämnes fält. Minskningen är att se till att egenskapen hostNameInCertificate är inställd på *. database.windows.net. Mer information om hur du anger egenskapen hostNameInCertificate finns i [ansluta med SSL-kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående minskning inte fungerar kan du skicka en supportbegäran för SQL Database med hjälp av följande URL: https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure SQL Connectivity-arkitekturen](sql-database-connectivity-architecture.md)
