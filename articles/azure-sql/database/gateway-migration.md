---
title: Meddelande om migrering av Gateway-trafik
description: Artikeln innehåller information om migrering av Azure SQL Database Gateway-IP-adresser
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1 
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 30e57736d0b0e40eb01573d6acca2c618dcf6ee3
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759707"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

När Azure-infrastrukturen förbättras kommer Microsoft regelbundet att uppdatera maskin varan så att vi ger bästa möjliga kund upplevelse. Under de kommande månaderna planerar vi att lägga till gatewayer som skapats på nyare maskin varu generationer, migrera trafik till dem och sedan göra inprovisionade gatewayer byggda på äldre maskin vara i vissa regioner.  

Kunderna får ett meddelande via e-post och i Azure Portal väl i förväg om eventuella ändringar av gatewayer som är tillgängliga i varje region. Den senaste informationen kommer att behållas i tabellen [Azure SQL Database Gateway IP-adresser](connectivity-architecture.md#gateway-ip-addresses) .

## <a name="status-updates"></a>Status uppdateringar

# <a name="in-progress"></a>[Pågår](#tab/in-progress-ip)
### <a name="september-2020"></a>September 2020

Nya SQL-gatewayer läggs till i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den 10 september 2020:

- Västra centrala USA: 13.78.248.43 
- Södra Afrika: 102.133.120.2  

Nya SQL-gatewayer läggs till i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den 1 september 2020:

- Nord Europa: 13.74.104.113 
- Västra 2; USA: 40.78.248.10 
- Västeuropa: 52.236.184.163 
- Södra centrala USA: 20.45.121.1, 20.49.88.1 

Befintliga SQL-gatewayer börjar att acceptera trafik i följande regioner. Dessa SQL-gatewayer ska börja acceptera kund trafik den 1 september 2020. :
- Japan, öst: 40.79.184.8, 40.79.192.5

### <a name="august-2020"></a>2020 augusti

Nya SQL-gatewayer läggs till i följande regioner:

- Östra Australien: 13.70.112.9
- Kanada, centrala: 52.246.152.0, 20.38.144.1 
- USA, västra 2:40.78.240.8

Dessa SQL-gatewayer ska börja acceptera kund trafik den 10 augusti 2020. 

# <a name="completed"></a>[Slutförd](#tab/completed-ip)

Följande Gateway-migreringar har slutförts: 

### <a name="october-2019"></a>Oktober 2019
- Brasilien, södra
- USA, västra
- Europa, västra
- East US
- Central US
- Sydostasien
- USA, södra centrala
- Norra Europa
- USA, norra centrala
- Japan, västra
- Japan, östra
- USA, östra 2
- Asien, östra

---

## <a name="impact-of-this-change"></a>Effekt av den här ändringen

Trafik migreringen kan ändra den offentliga IP-adress som DNS matchar för din databas i Azure SQL Database.
Du kan påverkas om du:

- Hårdkoda IP-adressen för en viss gateway i din lokala brand vägg
- Ha undernät som använder Microsoft. SQL som en tjänst slut punkt men inte kan kommunicera med IP-adresser för gateway
- Använd [zonens redundanta konfiguration](high-availability-sla.md#zone-redundant-configuration) för din databas

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
