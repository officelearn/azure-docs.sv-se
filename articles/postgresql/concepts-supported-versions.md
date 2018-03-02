---
title: "Versioner som stöds i Azure-databas för PostgreSQL"
description: "Beskriver versionerna som stöds i Azure-databas för PostgreSQL."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 2065631922d25deaa94601484da9b8de3fd62b22
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="supported-postgresql-database-versions"></a>Versioner stöds PostgreSQL-databas
Microsoft syftet är att stödja n-2 versioner av PostgreSQL-motorn i Azure-databasen för PostgreSQL-tjänsten, vilket innebär att den släppta huvudversionen (n) och två tidigare större versioner (-2).

Azure-databas för PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-962"></a>PostgreSQL Version 9.6.2
Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-2.html) för mer information om förbättringar och korrigeringar i PostgreSQL 9.6.2.

## <a name="postgresql-version-957"></a>PostgreSQL Version 9.5.7
Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-7.html) mer information om förbättringar och korrigeringar i PostgreSQL 9.5.7.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure-databas för PostgreSQL hanterar automatiskt korrigering för delversion uppdateringar. För närvarande i förhandsversion stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns [PostgreSQL-tillägg](concepts-extensions.md)
