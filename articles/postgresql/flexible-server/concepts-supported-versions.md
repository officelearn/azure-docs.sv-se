---
title: Versioner som stöds – Azure Database for PostgreSQL-flexibel Server
description: Beskriver de PostgreSQL huvud-och del versioner som stöds i Azure Database for PostgreSQL-flexibel Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: b139e2f14379e0cd52ca72c347d49bcb4512c473
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542088"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL huvud versioner som stöds i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-12"></a>PostgreSQL version 12

Den aktuella del versionen är 12,4. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/12/static/release-12-4.html) .

## <a name="postgresql-version-11"></a>PostgreSQL-version 11

Den aktuella del versionen är 11,9. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-9.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 och äldre

Vi stöder inte PostgreSQL version 10 och äldre för Azure Database for PostgreSQL-flexibel Server. Använd distributions alternativet för [enskild server](../concepts-supported-versions.md) om du behöver äldre versioner.

## <a name="managing-upgrades"></a>Hantera uppgraderingar

PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL automatiskt korrigering av servrar med mindre versioner under tjänstens månatliga distributioner.

Automation för större versions uppgradering stöds inte ännu. Till exempel finns det för närvarande ingen automatisk uppgradering från PostgreSQL 11 till PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->