---
title: Versioner som stöds - Azure Database för MySQL
description: Ta reda på vilka versioner av MySQL-servern som stöds i Azure Database for MySQL-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536980"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure Database för MySQL-serverversioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med innoDB-motorn.

MySQL använder namnschemat X.Y.Z. X är huvudversionen, Y är den delversion, och Z är buggfix release. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten.

Azure Database för MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-56"></a>MySQL version 5.6

Bug fix release: 5.6.45

Se MySQL-viktig [information](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) om du vill veta mer om förbättringar och korrigeringar i den här versionen.

## <a name="mysql-version-57"></a>MySQL version 5.7

Bug fix release: 5.7.27

Se MySQL-viktig [information](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) om du vill veta mer om förbättringar och korrigeringar i den här versionen.

## <a name="mysql-version-80"></a>MySQL version 8.0

Bug fix release: 8.0.15

Se MySQL-viktig [information](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) om du vill veta mer om förbättringar och korrigeringar i den här versionen.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigering för buggfixversionsuppdateringar. Till exempel 5.7.20 till 5.7.21.  

För närvarande stöds inte uppgradering av lägre och högre versioner. Det finns till exempel inte stöd för att uppgradera från MySQL 5.6 till MySQL 5.7. Om du vill uppgradera från 5.6 till 5.7 skapar du en [dump och återställer](./concepts-migrate-dump-restore.md) den till en server som skapades med den nya motorversionen.

## <a name="next-steps"></a>Nästa steg

Information om specifika resurskvoter och begränsningar baserat på **tjänstnivån**finns i [Tjänstnivåer](./concepts-pricing-tiers.md)
