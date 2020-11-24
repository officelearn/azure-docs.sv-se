---
title: Versioner som stöds – Azure Database for MySQL
description: Lär dig vilka versioner av MySQL-servern som stöds i Azure Database for MySQLs tjänsten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751036"
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

Huvud versions uppgradering stöds för närvarande av tjänsten för uppgraderingar från MySQL v 5.6 till v 5.7. Mer information finns [i så här utför du viktiga uppgraderingar av versioner](how-to-major-version-upgrade.md). Om du vill uppgradera från 5,7 till 8,0 rekommenderar vi att du utför [dumpning och återställning](./concepts-migrate-dump-restore.md) till en server som har skapats med den nya motor versionen.

## <a name="next-steps"></a>Nästa steg

- Mer information kring Azure Database for MySQL versions policy finns i [det här dokumentet](concepts-version-policy.md).
- Information om vilka resurs kvoter och begränsningar som baseras på **tjänst nivån** finns i [tjänst nivåer](./concepts-pricing-tiers.md)
