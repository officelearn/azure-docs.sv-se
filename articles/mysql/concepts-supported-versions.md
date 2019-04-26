---
title: Versioner som stöds i Azure Database for MySQL
description: Beskriver versionerna som stöds i Azure Database för MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/13/2019
ms.openlocfilehash: e7e81632b2be135fb74d375ab8a11f1b4b3ef39d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525922"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Stöds Azure Database för MySQL server-versioner

Azure Database för MySQL har utvecklats från [MySQL Community Edition](https://www.mysql.com/products/community/), InnoDB-motorn.

MySQL använder X.Y.Z namngivningsschemat. X är den högre versionen, Y är den lägre versionen och Z är den felkorrigering-versionen. Mer information om schemat finns i den [MySQL-dokumentation](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database för MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-56"></a>MySQL-Version 5.6

Felkorrigering version: 5.6.42

Referera till MySQL [viktig](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) mer information om förbättringar och korrigeringar i MySQL 5.6.42.

## <a name="mysql-version-57"></a>MySQL-Version 5.7

Felkorrigering version: 5.7.24

Referera till MySQL [viktig](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) vill veta mer om förbättringar och korrigeringar i MySQL 5.7.24.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats, visar MySQL-klienten versionen av MySQL i gatewayen, inte den faktiska versionen som körs på din MySQL-server-instans. Om du vill kontrollera vilken version av MySQL-serverinstans, använda den `SELECT VERSION();` i Kommandotolken MySQL.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för felkorrigering versionuppdateringar. Till exempel 5.7.20 5.7.21.  

För närvarande kan stöds små och stora versionsuppgraderingar inte. Till exempel stöds uppgradering från MySQL 5.6 till MySQL 5.7 inte. Om du vill uppgradera från 5.6 till 5.7 kan ta en [dumpa och Återställ](./concepts-migrate-dump-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg

Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-pricing-tiers.md)
