---
title: Versioner som stöds i Azure-databas för PostgreSQL
description: Beskriver versionerna som stöds i Azure-databas för PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 821273f3f9b4fb1f617f9b9e5268d819396df2b0
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726405"
---
# <a name="supported-postgresql-database-versions"></a>Versioner stöds PostgreSQL-databas
Microsoft syftet är att stödja n-2 versioner av PostgreSQL-motorn i Azure-databasen för PostgreSQL-tjänsten, vilket innebär att den släppta huvudversionen (n) och två tidigare större versioner (-2).

Azure-databas för PostgreSQL stöder för närvarande följande versioner:

## <a name="postgresql-version-103"></a>PostgreSQL Version 10.3
Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/10/static/release-10-3.html) för mer information om förbättringar och korrigeringar i PostgreSQL 10.3.

## <a name="postgresql-version-967"></a>PostgreSQL Version 9.6.7
Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/9.6/static/release-9-6-7.html) för mer information om förbättringar och korrigeringar i PostgreSQL 9.6.7.

## <a name="postgresql-version-9511"></a>PostgreSQL Version 9.5.11
Referera till den [PostgreSQL dokumentationen](https://www.postgresql.org/docs/9.5/static/release-9-5-11.html) mer information om förbättringar och korrigeringar i PostgreSQL 9.5.11.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Azure-databas för PostgreSQL hanterar automatiskt korrigering för delversion uppdateringar. För närvarande stöds huvudversion uppgradering inte. Till exempel stöds uppgradera från PostgreSQL 9.5 till PostgreSQL 9.6 inte.

## <a name="next-steps"></a>Nästa steg
Mer information om stöd för olika PostgreSQL-tillägg finns [PostgreSQL tillägg](concepts-extensions.md).
