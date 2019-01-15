---
title: Tabellen granskning, TDS-omdirigering och IP-slutpunkter för Azure SQL Database | Microsoft Docs
description: Läs mer om granskning, TDS-omdirigering och IP-slutpunkten ändringar när du implementerar tabellgranskning i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 607351718ddffbea5fa4dbbae333d81aac230f9a
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258959"
---
# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-table-auditing"></a>Stöd för SQL Database - klientversioner och IP för Tabellgranskning

> [!IMPORTANT]
> Det här dokumentet gäller enbart för Tabellgranskning som är **nu föråldrade**.<br>
> Använd den nya [Blobbgranskning](sql-database-auditing.md) metod, som **inte** kräver ändringar för äldre klienten anslutning sträng. Ytterligare information om Blobgranskning finns i [Kom igång med SQL-databasgranskning](sql-database-auditing.md).

[Databasen granskning](sql-database-auditing.md) fungerar automatiskt med SQL-klienter som stöder TDS-omdirigering. Observera att omdirigering inte gäller när du använder metoden Blobbgranskning.

## <a id="subheading-1"></a>Stöd för äldre klienter
Alla klienter som implementerar TDS 7.4 bör också stöd för omdirigering. Undantag till detta inkluderar JDBC 4.0 där funktionen omdirigering stöds inte fullt ut och Tedious för Node.JS i vilka omdirigering har inte implementerats.

För ”äldre klienter”, ska d.v.s. vilka support TDS 7.3 och under - serverns FQDN i anslutningen versionssträng ändras:

Ursprungliga serverns FQDN i anslutningssträngen: <*servernamn*>. database.windows.net

Ändrade serverns FQDN i anslutningssträngen: <*servernamn*> .database. **säker**. windows.net

En lista över ”klientversioner” innehåller:

* .NET 4.0 och nedan.
* ODBC-10.0 och nedan.
* JDBC (medan JDBC stöder TDS 7.4, funktion för TDS-omdirigering fullständigt stöds inte)
* Tedious (för Node.JS)

**Kommentar:** Den ovannämnda servern FQDN ändring kan vara användbart också för att tillämpa en princip för granskning för SQL Server-nivå utan att behöva ett konfigurationssteg i varje databas (tillfällig lösning).

## <a id="subheading-2"></a>IP-slutpunkten ändras när du aktiverar granskning
Observera att när du aktiverar Tabellgranskning, ändrar IP-slutpunkten för din databas. Om du har strikta brandväggsinställningar kan uppdatera dessa brandväggsinställningar därefter.

Den nya databas IP-slutpunkten beror på området:

| Databasen Region | Möjliga IP-slutpunkter |
| --- | --- |
| Norra Kina |139.217.29.176, 139.217.28.254 |
| Östra Kina |42.159.245.65, 42.159.246.245 |
| Östra Australien |104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Sydöstra Australien |191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Södra Brasilien |104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centrala USA |104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| USA, centrala – EUAP |52.180.178.16, 52.180.176.190 |
| Östasien |23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| USA, östra 2 |104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Östra USA |23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| USA, västra – EUAP |52.225.190.86, 52.225.191.187 |
| Indien, centrala |104.211.98.219, 104.211.103.71 |
| Södra Indien |104.211.227.102, 104.211.225.157 |
| Indien, västra |104.211.161.152, 104.211.162.21 |
| Östra Japan |104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Västra Japan |104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| Norra centrala USA |191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Norra Europa |104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Södra centrala USA |191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Sydostasien |104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Västra Europa |104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Västra USA |191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Västra USA 2 |13.66.224.156, 13.66.227.8 |
| Västra centrala USA |52.161.29.186, 52.161.27.213 |
| Centrala Kanada |13.88.248.106, 13.88.248.110 |
| Östra Kanada |40.86.227.82, 40.86.225.194 |
| Storbritannien, norra |13.87.101.18, 13.87.100.232 |
| Storbritannien, södra 2 |13.87.32.202, 13.87.32.226 |
