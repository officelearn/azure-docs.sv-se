---
title: "Minimal avbrottstid migrering till Azure-databas för PostgreSQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du utför en migrering med minimal avbrottstid genom att extrahera en PostgreSQL-databas i den här filen, återställa PostgreSQL-databas från en fil skapas av pg_dump i Azure-databas för PostgreSQL och ställa in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen med hjälp av Attunity replikera för Microsoft Migrations."
services: postgresql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 8c98c58042e7f1d1726eaad6ee03d1531b6c910e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Minimal avbrottstid migrering till Azure-databas för PostgreSQL
Du kan migrera dina befintliga PostgreSQL-databas till Azure-databas för PostgreSQL med hjälp av Attunity replikera för Microsoft Migrations, ett samordna sponsrad, gemensamma erbjudande från Attunity och från Microsoft som har angetts tillsammans med tjänsten Azure Database migrering utan extra kostnad för Microsoft-kunder. Attunity replikera för Microsoft Migrations kan också minsta avbrott i Databasmigrering, och källdatabasen fortsätter att köras under migreringsprocessen.

Attunity replikera är ett verktyg för replikering av data som möjliggör datasynkronisering mellan olika källor och mål, sprider schemat skapa skript och data som är associerade med varje databastabell. Attunity replikera inte spridas eventuella artefakter (till exempel SP utlösare, funktioner, etc.) eller konvertera, till exempel PL/SQL-kod som är värdbaserad i dessa artefakter att T-SQL.

> [!NOTE]
> Medan Attunity replikera stöder ett stort antal scenarier med migrering, fokuserar Attunity replikera för Microsoft Migrations på stöd för en delmängd av källan/målet par.

En översikt över processen för att utföra en migrering med minimal avbrottstid innehåller:

1. **Migrera PostgreSQL datakällans schema** till en Azure-databas för PostgreSQL-databas med hjälp av den [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) kommando med parametern - n och sedan använda den [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) kommando.

2. **Ställa in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen** med hjälp av Attunity replikera för Microsoft Migrations. Detta minimerar den tid som källdatabasen måste anges som skrivskyddade när du förbereder att växla dina program mål PostgreSQL-databas på Azure.

Mer information om Attunity replikera för Microsoft Migrations erbjudande visa följande resurser:
 - Attunity replikera för Microsoft Migrations [webbsida](https://aka.ms/attunity-replicate).
 - [Hämta](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity replikera för migrering av Microsoft.
 - Attunity replikera [Community](https://microsoft.attunity.com/), med en Snabbstartsguide, självstudier och support.
 - Stegvisa instruktioner om hur du använder Attunity för att migrera från PostgreSQL till Azure-databas för PostgreSQL avser den [databasen Migreringsguide](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).