---
title: Versioner som stöds i Azure Database for PostgreSQL
description: Beskriver versionerna som stöds i Azure Database för PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 5b06128979bf448a0b85084d5178d9291beb7691
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422073"
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
Azure Database för PostgreSQL hanterar automatiskt delversion korrigeringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte. Om du vill uppgradera till nästa större version, skapa en databas [dumpa och Återställ](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med ny version.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns i [PostgreSQL-tillägg](concepts-extensions.md).
