---
title: Versioner som stöds i Azure Database for PostgreSQL-enskild server
description: Beskriver de versioner som stöds i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551362"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL databas versioner som stöds
Microsoft syftar till att stödja n-2-versioner av PostgreSQL-motorn i Azure Database for PostgreSQL-Single-Server. Versionerna är den aktuella huvud versionen av Azure (n) och de två tidigare huvud versionerna (-2).

Azure Database for PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-112"></a>PostgreSQL-version 11,2
Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-2.html) .

## <a name="postgresql-version-107"></a>PostgreSQL-version 10,7
Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-7.html) .

## <a name="postgresql-version-9612"></a>PostgreSQL-version 9.6.12
Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) .

## <a name="postgresql-version-9516"></a>PostgreSQL-version 9.5.16
Läs postgresql- [dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) om du vill veta mer om förbättringar och korrigeringar i den här lägre versionen.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure Database for PostgreSQL hanterar automatiskt lägre versions uppdateringar. För närvarande stöds inte högre versions uppgradering. Det går till exempel inte att uppgradera från PostgreSQL 9,5 till PostgreSQL 9,6. Om du vill uppgradera till nästa högre version skapar du en databas [dumpning och återställer](./howto-migrate-using-dump-and-restore.md) den till en server som har skapats med den nya motor versionen.

> Observera att innan PostgreSQL version 10 ansågs versions [principen för postgresql](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att bli en ökning av det första _eller_ andra numret (till exempel 9,5 till 9,6 ansågs vara en _huvud_ versions uppgradering).
> Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering (till exempel 10,0 till 10,1 är en _lägre_ versions uppgradering och 10 till 11 är en _högre_ versions uppgradering).

## <a name="next-steps"></a>Nästa steg
Information om stöd för olika PostgreSQL-tillägg finns i [postgresql-tillägg](concepts-extensions.md).
