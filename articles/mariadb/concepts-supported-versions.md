---
title: Versioner som stöds - Azure Database för MariaDB
description: Ta reda på vilka versioner av MariaDB-servern som stöds i Azure Database for MariaDB-tjänsten.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527715"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Azure Database för MariaDB-serverversioner som stöds

Azure Database för MariaDB har utvecklats från [MariaDB Server](https://downloads.mariadb.org/)med öppen källkod med innoDB-motorn.

MariaDB använder namngivningsschemat X.Y.Z. X är huvudversionen, Y är den delversion, och Z är patchversionen.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till serverinstanser. När anslutningen har upprättats visar MySQL-klienten den version av MariaDB som angetts i gatewayen, inte den faktiska versionen som körs på din MariaDB-serverinstans. Om du vill ta reda på vilken `SELECT VERSION();` version av MariaDB-serverinstansen du vill använda kommandot.

Azure Database för MariaDB stöder för närvarande följande version:

## <a name="mariadb-version-102"></a>MariaDB Version 10.2

Patch version: 10.2.25

Se [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) om du vill veta mer om förbättringar och korrigeringar i den här versionen.

## <a name="mariadb-version-103"></a>MariaDB Version 10.3

Patch version: 10.3.16

Se [MariaDB-dokumentationen](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) om du vill veta mer om förbättringar och korrigeringar i den här versionen.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt uppgraderingar för patchuppdateringar. Till exempel 10.2.21 till 10.2.23.  

För närvarande stöds inte uppgradering av lägre och högre versioner. Det finns till exempel inte stöd för att uppgradera från MariaDB 10.2 till MariaDB 10.3. Om du vill uppgradera från 10.2 till 10.3 tar du en [soptipp och återställer](./howto-migrate-dump-restore.md) den till en server som skapades med den nya motorversionen.

## <a name="next-steps"></a>Nästa steg

- Information om specifika resurskvoter och begränsningar baserat på **tjänstnivån**finns i [Tjänstnivåer](./concepts-pricing-tiers.md).
