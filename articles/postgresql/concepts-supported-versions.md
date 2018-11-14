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
ms.date: 11/12/2018
ms.openlocfilehash: 04e47e15206e22e7965121c6c277e4032ea04ddf
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621062"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-databas-versioner som stöds
Microsoft syftar till att stödja n-2 versioner av PostgreSQL-motorn i Azure Database for PostgreSQL-tjänsten. Versionerna som är den aktuella huvudversionen på Azure (n) och de två föregående största versionerna (-2).

Azure Database för PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-105"></a>PostgreSQL-Version 10,5
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/10/static/release-10-5.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-9610"></a>PostgreSQL-Version 9.6.10
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html) mer information om förbättringar och korrigeringar i den här minor-version.

## <a name="postgresql-version-9514"></a>PostgreSQL-Version 9.5.14
Referera till den [PostgreSQL-dokumentation](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html) vill veta mer om förbättringar och korrigeringar i den här minor-version.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure Database för PostgreSQL hanterar automatiskt delversion korrigeringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte. Om du vill uppgradera till nästa huvudversion kan ta en [dumpa och Återställ](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns i [PostgreSQL-tillägg](concepts-extensions.md).
