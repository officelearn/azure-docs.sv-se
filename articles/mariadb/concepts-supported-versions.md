---
title: Versioner som stöds i Azure Database for MariaDB
description: Beskriver versionerna som stöds i Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065729"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Stöds Azure Database for MariaDB server-versioner

Azure Database for MariaDB har utvecklats från den öppna källkoden [MariaDB Server](https://downloads.mariadb.org/), InnoDB-motorn. Azure Database for MariaDB stöder för närvarande följande version:

## <a name="mariadb-version-102"></a>MariaDB Version 10.2

Referera till den [MariaDB dokumentation](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) mer information om förbättringar och korrigeringar i MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>MariaDB Version 10.3

Referera till den [MariaDB dokumentation](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) mer information om förbättringar och korrigeringar i MariaDB 10.3.14.

> [!NOTE]
> I tjänsten används en gateway för att omdirigera anslutningarna till server-instanser. När anslutningen har upprättats, visar MySQL-klienten versionen av MariaDB som angetts i gatewayen, inte den faktiska versionen som körs på din MariaDB server-instans. Om du vill kontrollera vilken version av din MariaDB server-instans, använda den `SELECT VERSION();` i Kommandotolken MySQL.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar

Tjänsten hanterar automatiskt korrigeringar för delversion uppdateringar.

## <a name="next-steps"></a>Nästa steg

- Information om specifika resurskvoter och begränsningar baserat på din **tjänstnivå**, se [tjänstnivåer](./concepts-pricing-tiers.md).
