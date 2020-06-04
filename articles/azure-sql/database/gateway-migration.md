---
title: Meddelande om migrering av Gateway-trafik
description: Artikeln innehåller information om migrering av Azure SQL Database Gateway-IP-adresser
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: d9ec21657f871211df575b56ff56962aad3f5c88
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324724"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

När Azure-infrastrukturen förbättras kommer Microsoft regelbundet att uppdatera maskin varan så att vi ger bästa möjliga kund upplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som skapats på nyare maskin varu generationer, migrera trafik till dem och sedan göra inprovisionade gatewayer byggda på äldre maskin vara i vissa regioner.  

Kunderna får ett meddelande via e-post och i Azure Portal väl i förväg om eventuella ändringar av gatewayer som är tillgängliga i varje region. Den senaste informationen kommer att behållas i tabellen [Azure SQL Database Gateway IP-adresser](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="impact-of-this-change"></a>Effekt av den här ändringen

Den första avrundingen av trafikmigrering till nyare gateways har schemalagts för den **14 oktober 2019** i följande regioner:

- Brasilien, södra
- USA, västra
- Europa, västra
- USA, östra
- USA, centrala
- Sydostasien
- USA, södra centrala
- Europa, norra
- USA, norra centrala
- Japan, västra
- Japan, östra
- USA, östra 2
- Asien, östra

Trafikmigreringen kommer att ändra den offentliga IP-adress som DNS matchar för din databas i Azure SQL Database.
Du kommer att påverkas om du har:

- Hårdkoda IP-adressen för en viss gateway i din lokala brand vägg
- Undernät som använder Microsoft. SQL som en tjänst slut punkt men inte kan kommunicera med IP-adresser för gateway

Du påverkas inte om du har:

- Omdirigering som anslutnings princip
- Anslutningar till SQL Database inifrån Azure och med service märken
- Anslutningar som görs med stödda versioner av JDBC-drivrutinen för SQL Server kommer inte att se någon påverkan. Information om vilka JDBC-versioner som stöds finns i [Hämta Microsoft JDBC-drivrutin för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du gör om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [Gateway-IP-adresser](connectivity-architecture.md#gateway-ip-addresses) i regionen på TCP-port 1433 och port intervall 11000-11999. Den här rekommendationen gäller för klienter som ansluter lokalt och som också ansluter via tjänst slut punkter. Mer information om Port intervall finns i [anslutnings princip](connectivity-architecture.md#connection-policy).

Anslutningar som görs från program som använder Microsoft JDBC driv rutinen under version 4,0 kan inte verifiera certifikat verifiering. Lägre versioner av Microsoft JDBC förlitar sig på eget namn (CN) i certifikatets ämnes fält. Minskningen är att se till att egenskapen hostNameInCertificate är inställd på *. database.windows.net. Mer information om hur du ställer in egenskapen hostNameInCertificate finns i [ansluta med kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående minskning inte fungerar kan du skicka en supportbegäran för SQL Database-eller SQL-hanterad instans med hjälp av följande URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure SQL Connectivity-arkitekturen](connectivity-architecture.md)
