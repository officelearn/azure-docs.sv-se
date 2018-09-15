---
title: Versioner som stöds i Azure Database for MySQL
description: Beskriver versionerna som stöds i Azure Database för MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: 1b6dded1521489353e65b630ef5432ba6ff8f3e8
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631573"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Stöds Azure Database för MySQL server-versioner
Azure Database för MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/), InnoDB-motorn.  Azure Database för MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-5639"></a>MySQL-Version 5.6.39
Referera till MySQL [dokumentation](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) mer information om förbättringar och korrigeringar i MySQL 5.6.39.

## <a name="mysql-version-5721"></a>MySQL-Version 5.7.21
Referera till MySQL [dokumentation](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) vill veta mer om förbättringar och korrigeringar i MySQL 5.7.21.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats, visar MySQL-klienten versionen av MySQL i gatewayen, inte den faktiska versionen som körs på din MySQL-server-instans. Om du vill kontrollera vilken version av MySQL-serverinstans, använda den `SELECT VERSION();` i Kommandotolken MySQL.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för delversion uppdateringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradering från MySQL 5.6 till MySQL 5.7 inte. Om du vill uppgradera till nästa huvudversion kan ta en [dumpa och Återställ](./concepts-migrate-dump-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg

Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-pricing-tiers.md)
