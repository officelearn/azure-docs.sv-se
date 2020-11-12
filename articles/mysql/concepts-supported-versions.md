---
title: Versioner som stöds – Azure Database for MySQL
description: Lär dig vilka versioner av MySQL-servern som stöds i Azure Database for MySQLs tjänsten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: ed767055c1227d39fc29ac77a44c96b19030ad28
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535868"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Azure Database for MySQL Server versioner som stöds

Azure Database for MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av InnoDB-motorn.

MySQL använder namngivnings schemat X. Y. Z. X är huvud versionen, Y är den lägre versionen och Z är den version av fel korrigeringen. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MySQL som har angetts i gatewayen, inte den faktiska versionen som körs på MySQL-serverinstansen. Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten.

Azure Database for MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-56"></a>MySQL version 5,6

Fel korrigerings version: 5.6.47

Se versions [anteckningar](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) för MySQL för att lära dig mer om förbättringar och korrigeringar i den här versionen.

## <a name="mysql-version-57"></a>MySQL version 5,7

Fel korrigerings version: 5.7.29

Se versions [anteckningar](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) för MySQL för att lära dig mer om förbättringar och korrigeringar i den här versionen.

## <a name="mysql-version-80"></a>MySQL version 8,0

Fel korrigerings version: 8.0.15

Se versions [anteckningar](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) för MySQL för att lära dig mer om förbättringar och korrigeringar i den här versionen.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för fel korrigering av versions uppdateringar. Till exempel 5.7.20 till 5.7.21.  

För närvarande stöds inte uppgradering av lägre och högre versioner. Det finns till exempel inte stöd för att uppgradera från MySQL 5.6 till MySQL 5.7. Om du vill uppgradera från 5.6 till 5.7 skapar du en [dump och återställer](./concepts-migrate-dump-restore.md) den till en server som skapades med den nya motorversionen.

## <a name="next-steps"></a>Nästa steg

- Mer information kring Azure Database for MySQL versions policy finns i [det här dokumentet](concepts-version-policy.md).
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](./concepts-pricing-tiers.md)
