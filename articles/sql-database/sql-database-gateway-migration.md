---
title: Meddelande om trafikmigrering av gateway
description: Artikel ger meddelande till användare om migreringen av AZURE SQL Database Gateways IP-adresser
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 07/01/2019
ms.openlocfilehash: 9e3c33bb7493f07d9fbf19710f21d0114e7abec8
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757081"
---
# <a name="azure-sql-database-traffic-migration-to-newer-gateways"></a>Azure SQL Database trafikmigrering till nyare gateways

När Azure-infrastrukturen förbättras uppdaterar Microsoft regelbundet maskinvaran för att säkerställa att vi ger bästa möjliga kundupplevelse. Under de kommande månaderna planerar vi att lägga till gateways som bygger på nyare maskinvarugenerationer, migrera trafik till dem och så småningom inaktivera gateways som bygger på äldre maskinvara i vissa regioner.  

Kunder kommer att meddelas via e-post och i Azure-portalen i god tid före eventuella ändringar av gateways som är tillgängliga i varje region. Den senaste informationen kommer att underhållas i tabellen [Azure SQL Database gateway IP-adresser.](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses)

## <a name="impact-of-this-change"></a>Effekten av denna förändring

Den första omgången av trafikmigrering till nyare gateways är planerad till **den 14 oktober 2019** i följande regioner:
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

Trafikmigrering ändrar den offentliga IP-adress som DNS löser för din SQL-databas.
Du kommer att påverkas om du har:
- Hårdkodade IP-adressen för en viss gateway i din lokala brandvägg
- Alla undernät som använder Microsoft.SQL som tjänstslutpunkt men kan inte kommunicera med gateway-IP-adresserna

Du kommer inte att påverkas om du har:
- Omdirigering som anslutningsprincip
- Anslutningar till SQL Database inifrån Azure och med hjälp av tjänsttaggar
- Anslutningar som görs med hjälp av versioner av JDBC-drivrutinen för SQL Server som stöds visas ingen inverkan. JDBC-versioner som stöds finns i [Hämta Microsoft JDBC Driver för SQL Server](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server).

## <a name="what-to-do-you-do-if-youre-affected"></a>Vad du ska göra om du påverkas

Vi rekommenderar att du tillåter utgående trafik till IP-adresser för alla [AZURE SQL Database gateway IP-adresser](sql-database-connectivity-architecture.md#azure-sql-database-gateway-ip-addresses) i regionen på TCP-port 1433 och portintervall 11000-11999. Den här rekommendationen gäller klienter som ansluter från lokala och även de som ansluter via Service-slutpunkter. Mer information om portintervall finns i [Anslutningsprincip](sql-database-connectivity-architecture.md#connection-policy).

Anslutningar som görs från program som använder Microsoft JDBC Driver nedan version 4.0 kan misslyckas certifikatvalidering. Lägre versioner av Microsoft JDBC är beroende av common name (CN) i fältet Ämne i certifikatet. Begränsningen är att säkerställa att egenskapen hostNameInCertificate är inställd på *.database.windows.net. Mer information om hur du anger egenskapen hostNameInCertificate finns i [Ansluta med kryptering](/sql/connect/jdbc/connecting-with-ssl-encryption).

Om ovanstående begränsning inte fungerar kan du lämna in en supportbegäran för SQL Database med följande URL:https://aka.ms/getazuresupport

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure SQL Connectivity Architecture](sql-database-connectivity-architecture.md)
