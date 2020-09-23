---
title: Versioner som stöds – Azure Database for PostgreSQL-flexibel Server
description: Beskriver de postgres huvud-och del versioner som stöds i Azure Database for PostgreSQL-flexibel Server.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936972"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>PostgreSQL huvud versioner som stöds i Azure Database for PostgreSQL-flexibel Server

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder för närvarande följande huvud versioner:

## <a name="postgresql-version-12"></a>PostgreSQL version 12

Den aktuella del versionen är 12,1. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/12/static/release-12-1.html) .

## <a name="postgresql-version-11"></a>PostgreSQL-version 11

Den aktuella del versionen är 11,8. Läs mer om förbättringar och korrigeringar i den här mindre versionen i [postgresql-dokumentationen](https://www.postgresql.org/docs/11/static/release-11-8.html) .

## <a name="postgresql-version-10-and-older"></a>PostgreSQL version 10 och äldre

Vi stöder inte PostgreSQL version 10 och äldre för Azure Database for PostgreSQL-flexibel Server. Använd distributions alternativet för [enskild server](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) om du behöver äldre versioner.

## <a name="managing-upgrades"></a>Hantera uppgraderingar

PostgreSQL-projektet utfärdar regelbundet mindre versioner för att åtgärda rapporterade buggar. Azure Database for PostgreSQL automatiskt korrigering av servrar med mindre versioner under tjänstens månatliga distributioner.

Automatisk uppgradering av högre version stöds inte ännu. Till exempel finns det för närvarande ingen automatisk uppgradering från PostgreSQL 11 till PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
