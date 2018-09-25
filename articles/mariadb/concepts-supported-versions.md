---
title: Versioner som stöds i Azure Database for MariaDB
description: Beskriver versionerna som stöds i Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977510"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Stöds Azure Database for MariaDB server-versioner
Azure Database for MariaDB har utvecklats från den öppna källkoden [MariaDB Server](https://downloads.mariadb.org/), InnoDB-motorn. Azure Database for MariaDB stöder för närvarande följande version:

## <a name="mariadb-version-10217"></a>MariaDB Version 10.2.17
Referera till den [MariaDB dokumentation](https://downloads.mariadb.org/mariadb/10.2.17/) mer information om förbättringar och korrigeringar i MariaDB 10.2.17.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats, visar MySQL-klienten versionen av MariaDB som angetts i gatewayen, inte den faktiska versionen som körs på din MariaDB server-instans. Om du vill kontrollera vilken version av din MariaDB server-instans, använda den `SELECT VERSION();` i Kommandotolken MySQL.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för delversion uppdateringar.

## <a name="next-steps"></a>Nästa steg
Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-pricing-tiers.md)