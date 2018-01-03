---
title: "Online migrering till Azure-databas för PostgreSQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du utför en online-migrering genom att extrahera en PostgreSQL-databas till en dumpfil återställa PostgreSQL-databas från en fil som skapas av pg_dump i Azure-databas för PostgreSQL och ställa in första last och kontinuerliga datasynkronisering från källdatabasen till måldatabasen med hjälp av Attunity replikera för Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: cf17ad87b33ed6196d86cebaf6efe699a542bf39
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="online-migration-to-azure-database-for-postgresql"></a>Online migrering till Azure-databas för PostgreSQL
Du kan migrera dina befintliga PostgreSQL-databas till Azure-databas för PostgreSQL med hjälp av Attunity replikera för Microsoft Migrations, ett samordna sponsrad, gemensamma erbjudande från Attunity och från Microsoft som har angetts tillsammans med tjänsten Azure Database migrering utan extra kostnad för Microsoft-kunder. Attunity replikera för Microsoft Migrations kan också minsta avbrott i Databasmigrering, och källdatabasen fortsätter att köras under migreringsprocessen.

Attunity replikera är ett verktyg för replikering av data som möjliggör datasynkronisering mellan olika källor och mål, sprider schemat skapa skript och data som är associerade med varje databastabell. Attunity replikera inte spridas eventuella artefakter (till exempel SP utlösare, funktioner, etc.) eller konvertera, till exempel PL/SQL-kod som är värdbaserad i dessa artefakter att T-SQL.

> [!NOTE]
> Medan Attunity replikera stöder ett stort antal scenarier med migrering, fokuserar Attunity replikera för Microsoft Migrations på stöd för en delmängd av källan/målet par.

Innehåller en översikt över processen för att utföra en online-migrering:

1. **Migrera PostgreSQL datakällans schema** till Azure-databas för PostgreSQL med hjälp av [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) och [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) med parametern - n.

2. **Ställ in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen** med hjälp av Attunity replikera för Microsoft Migrations. Detta minimerar så länge källdatabasen måste anges som skrivskyddade samtidigt som du förbereder att växla dina program mål PostgreSQL-databas på Azure.

Mer information om Attunity replikera för Microsoft Migrations erbjudande visa följande resurser:
 - Attunity replikera för Microsoft Migrations [webbsida](https://aka.ms/attunity-replicate).
 - [Hämta](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity replikera för migrering av Microsoft.
 - Stegvisa instruktioner om hur du använder Attunity för att migrera från PostgreSQL till Azure-databas för PostgreSQL avser den [databasen Migreringsguide](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).