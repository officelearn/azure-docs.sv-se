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
ms.date: 05/23/2018
ms.openlocfilehash: 4402867c93d9eb3f0d11a156da6045e758ac661a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34639800"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure-databas som stöds för MySQL server-versioner
Azure MySQL-databas har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/), InnoDB-motorn.  Azure-databas för MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-5639"></a>MySQL-Version 5.6.39
Referera till MySQL [dokumentationen](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) för mer information om förbättringar och korrigeringar i MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL-Version 5.7.21
Referera till MySQL [dokumentationen](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) mer information om förbättringar och korrigeringar i MySQL 5.7.21.

> [!NOTE]
> En gateway används i tjänsten, att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats visas MySQL-klienten version av MySQL i gatewayen, inte den faktiska versionen som körs på din MySQL server-instans. Använd för att avgöra versionen av du MySQL server-instansen av `SELECT VERSION();` kommandot MySQL i Kommandotolken. 

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigering för delversion uppdateringar. Huvudversion uppgraderingar stöds inte (t.ex. uppgradera från MySQL 5.6 till MySQL 5.7).

## <a name="next-steps"></a>Nästa steg

Information om specifik resurs kvoter och begränsningar baserat på din **tjänstnivån**, se [tjänstnivåer](./concepts-pricing-tiers.md)
