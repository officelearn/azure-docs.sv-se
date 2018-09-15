---
title: Versioner som stöds i Azure Database for PostgreSQL
description: Beskriver versionerna som stöds i Azure Database för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630602"
---
# <a name="supported-postgresql-database-versions"></a>Versioner som stöds PostgreSQL-databas
Microsoft syftar till att stödja n-2 versioner av PostgreSQL-motorn i Azure Database for PostgreSQL-tjänsten, vilket innebär att den släppta huvudversionen (n) och de två föregående största versionerna (-2).

Azure Database för PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-104"></a>PostgreSQL-Version 10.4
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/10/static/release-10-4.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-969"></a>PostgreSQL-Version 9.6.9
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-9513"></a>PostgreSQL-Version 9.5.13
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) vill veta mer om förbättringar och korrigeringar i den här minor-version.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure Database för PostgreSQL hanterar automatiskt korrigeringar för delversion uppdateringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte. Om du vill uppgradera till nästa huvudversion kan ta en [dumpa och Återställ](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns i [PostgreSQL-tillägg](concepts-extensions.md).
