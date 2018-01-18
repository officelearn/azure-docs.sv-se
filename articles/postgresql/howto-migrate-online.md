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
ms.openlocfilehash: 429828515411986dc7b3586360cb9923a5bb20de
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-postgresql"></a>Minimal avbrottstid migrering till Azure-databas för PostgreSQL
Du kan migrera dina befintliga PostgreSQL-databas till Azure-databas för PostgreSQL med hjälp av Attunity replikera för Microsoft Migrations. Attunity replikera är en gemensam erbjudande från Attunity och Microsoft. Tillsammans med Azure för migrering databastjänsten ingår det utan extra kostnad för Microsoft-kunder. 

Attunity replikera bidrar till att minimera avbrottstid vid migrering av databasen och den bevarar källdatabasen operativa under hela processen.

Attunity replikera är ett verktyg för replikering av data som gör det möjligt för datasynkronisering mellan olika källor och mål. Den sprider schema-skriptet och data som är associerade med varje databastabell. Inte att sprida Attunity replikera eventuella artefakter (till exempel SP, utlösare, funktioner och så vidare) eller konvertera, till exempel, PL/SQL-kod som finns i dessa artefakter att T-SQL.

> [!NOTE]
> Den fokuserar på stöd för en delmängd av källan/målet par Attunity replikera stöder ett stort antal scenarier med migrering.

En översikt över processen för att utföra en migrering med minimal avbrottstid innehåller:

* **Migrera PostgreSQL datakällans schema** till en Azure-databas för PostgreSQL-databas med hjälp av den [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) kommando med parametern - n och sedan använda den [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) kommando.

* **Ställa in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen** med hjälp av Attunity replikera för Microsoft Migrations. Detta minimerar den tid som källdatabasen måste anges som skrivskyddade när du förbereder att växla dina program mål PostgreSQL-databas på Azure.

Mer information om Attunity replikera för Microsoft Migrations erbjudande finns i följande resurser:
 - Gå till den [Attunity replikera för Microsoft Migrations](https://aka.ms/attunity-replicate) webbsidan.
 - Hämta [Attunity replikera för Microsoft migreringar](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Gå till den [Attunity replikera Community](https://microsoft.attunity.com/) för en Snabbstartsguide, självstudier och support.
 - Detaljerade anvisningar om hur du använder Attunity replikera för att migrera från PostgreSQL till Azure-databas för PostgreSQL finns i [databasen Migreringsguide](https://datamigration.microsoft.com/scenario/postgresql-to-azurepostgresql).