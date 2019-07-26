---
title: Versioner som stöds i Azure Database for MySQL
description: Beskriver de versioner som stöds i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 7/25/2019
ms.openlocfilehash: 3d4bab4558ebfd0f6031ef00a0b67bb0d5b61120
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501451"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure Database for MySQL Server versioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av InnoDB-motorn.

MySQL använder namngivnings schemat X. Y. Z. X är huvud versionen, Y är den lägre versionen och Z är den version av fel korrigeringen. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till Server instanser. När anslutningen har upprättats visar MySQL-klienten versionen av MySQL-uppsättningen i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Ta reda på versionen för MySQL Server-instansen genom `SELECT VERSION();` att använda kommandot i MySQL-prompten.

Azure Database for MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-56"></a>MySQL version 5,6

Versions korrigerings version: 5.6.42

Se [versions anteckningar](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) för MySQL om du vill veta mer om förbättringar och korrigeringar i MySQL 5.6.42.

## <a name="mysql-version-57"></a>MySQL version 5,7

Versions korrigerings version: 5.7.24

Se [versions anteckningar](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) för MySQL om du vill veta mer om förbättringar och korrigeringar i MySQL 5.7.24.

## <a name="mysql-version-80"></a>MySQL version 8,0

> [!NOTE]
> MySQL 8,0 är för närvarande en för hands version. Om du inte ser MySQL 8,0 i Azure Portal kanske distributionen inte har slutförts i din region. 

Versions korrigerings version: 8.0.15

Se [versions anteckningar](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) för MySQL om du vill veta mer om förbättringar och korrigeringar i MySQL 8.0.15.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för fel korrigering av versions uppdateringar. Till exempel 5.7.20 till 5.7.21.  

För närvarande stöds inte mindre och större versions uppgraderingar. Det finns till exempel inte stöd för att uppgradera från MySQL 5,6 till MySQL 5,7. Om du vill uppgradera från 5,6 till 5,7 tar du en [dump och återställer](./concepts-migrate-dump-restore.md) den till en server som har skapats med den nya motor versionen.

## <a name="next-steps"></a>Nästa steg

Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån**finns i [tjänst nivåer](./concepts-pricing-tiers.md)
