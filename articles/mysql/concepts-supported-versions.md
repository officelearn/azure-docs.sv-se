---
title: Versioner som stöds i Azure för MySQL-databas
description: Beskriver versionerna som stöds i Azure-databas för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: 53c8d51ddf9b7465a99b8b0685d7f6ce177fc526
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure-databas som stöds för MySQL server-versioner
Azure MySQL-databas har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/), InnoDB-motorn.  Azure-databas för MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-5638"></a>MySQL Version 5.6.38
Referera till MySQL [dokumentationen](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) för mer information om förbättringar och korrigeringar i MySQL 5.6.38.

## <a name="mysql-version-5720"></a>MySQL Version 5.7.20
Referera till MySQL [dokumentationen](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.html) mer information om förbättringar och korrigeringar i MySQL 5.7.20.

> [!NOTE]
> En gateway används i tjänsten, att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats visas MySQL-klienten version av MySQL i gatewayen, inte den faktiska versionen som körs på din MySQL server-instans. Använd för att avgöra versionen av du MySQL server-instansen av `SELECT VERSION();` kommandot MySQL i Kommandotolken. 

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigering för delversion uppdateringar. Huvudversion uppgraderingar stöds inte (t.ex. uppgradera från MySQL 5.6 till MySQL 5.7).

## <a name="next-steps"></a>Nästa steg

Information om specifik resurs kvoter och begränsningar baserat på din **tjänstnivån**, se [tjänstnivåer](./concepts-pricing-tiers.md)
