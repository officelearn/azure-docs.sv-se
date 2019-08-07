---
title: Versioner som stöds i Azure Database for PostgreSQL-enskild server
description: Beskriver de versioner som stöds i Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837893"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL databas versioner som stöds
Microsoft syftar till att stödja n-2-versioner av PostgreSQL-motorn i Azure Database for PostgreSQL-Single-Server. Versionerna är den aktuella huvud versionen av Azure (n) och de två tidigare huvud versionerna (-2).

Azure Database for PostgreSQL stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-11"></a>PostgreSQL-version 11
Den aktuella del versionen är 11,4. Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-4.html) .

## <a name="postgresql-version-10"></a>PostgreSQL version 10
Den aktuella del versionen är 10,9. Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/10/static/release-10-9.html) .

## <a name="postgresql-version-96"></a>PostgreSQL-version 9,6
Den aktuella del versionen är 9.6.14. Läs mer om förbättringar och korrigeringar i den här lägre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) .

## <a name="postgresql-version-95"></a>PostgreSQL-version 9,5
Den aktuella del versionen är 9.5.18. Läs postgresql- [dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) om du vill veta mer om förbättringar och korrigeringar i den här lägre versionen.

## <a name="managing-upgrades"></a>Hantera uppgraderingar
Azure Database for PostgreSQL hanterar automatiskt lägre versions uppgraderingar. 

Automatisk uppgradering av huvud version stöds inte. Det finns till exempel ingen automatisk uppgradering från PostgreSQL 9,5 till PostgreSQL 9,6. Om du vill uppgradera till nästa högre version skapar du en [databas dumpning och återställer](./howto-migrate-using-dump-and-restore.md) till en server som har skapats med den nya motor versionen.

### <a name="version-syntax"></a>Versions syntax
Innan PostgreSQL version 10 anses den [postgresql versions principen](https://www.postgresql.org/support/versioning/) en _större versions_ uppgradering för att öka det första _eller_ andra numret. 9,5 till 9,6 ansågs till exempel vara en _huvud_ versions uppgradering. Från och med version 10 betraktas bara en ändring i det första talet som en huvud versions uppgradering. Till exempel är 10,0 till 10,1 en _lägre_ versions uppgradering. Version 10 till 11 är en _huvud_ versions uppgradering.

## <a name="next-steps"></a>Nästa steg
Information om PostgreSQL-tillägg som stöds finns i [tillägg-dokumentet](concepts-extensions.md).
