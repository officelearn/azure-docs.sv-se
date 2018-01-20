---
title: "Minimal avbrottstid migrering till Azure-databas för MySQL | Microsoft Docs"
description: "Den här artikeln beskriver hur du utför en minimal avbrottstid migrering av en MySQL-databas till Azure-databas för MySQL och hur du ställer in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen med hjälp av Attunity replikera för Microsoft Migreringar."
services: mysql
author: HJToland3
ms.author: jtoland
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 01/04/2018
ms.openlocfilehash: 3d7e96623ef4ad9abd55deca8b410e9ce0d73b6c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal avbrottstid migrering till Azure-databas för MySQL
Du kan migrera dina befintliga MySQL-databas till Azure-databas för MySQL med hjälp av Attunity replikera för Microsoft Migrations. Attunity replikera är en gemensam erbjudande från Attunity och Microsoft. Tillsammans med Azure för migrering databastjänsten ingår det utan extra kostnad för Microsoft-kunder. 

Attunity replikera bidrar till att minimera avbrottstid vid migrering av databasen och den bevarar källdatabasen operativa under hela processen.

Attunity replikera är ett verktyg för replikering av data som gör det möjligt för datasynkronisering mellan olika källor och mål. Den sprider schemat skapa skript och data som är associerade med varje databastabell. Inte att sprida Attunity replikera eventuella artefakter (till exempel SP, utlösare, funktioner och så vidare) eller konvertera, till exempel, PL/SQL-kod som finns i dessa artefakter att T-SQL.

> [!NOTE]
> Den fokuserar på stöd för en delmängd av källan/målet par Attunity replikera stöder ett stort antal scenarier med migrering.

En översikt över processen för att utföra en migrering med minimal avbrottstid innehåller:

* **Migrera MySQL datakällans schema** till en Azure-databas för MySQL hanteras database-tjänsten med hjälp av [MySQL arbetsstationen](https://www.mysql.com/products/workbench/).

* **Ställa in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen** med hjälp av Attunity replikera för Microsoft Migrations. Detta minimerar den tid som källdatabasen måste anges som skrivskyddade när du förbereder att växla dina program mål MySQL-databas på Azure.

Mer information om Attunity replikera för Microsoft Migrations erbjudande finns i följande resurser:
 - Gå till den [Attunity replikera för Microsoft Migrations](https://aka.ms/attunity-replicate) webbsidan.
 - Hämta [Attunity replikera för Microsoft migreringar](http://discover.attunity.com/download-replicate-microsoft-lp6657.html).
 - Gå till den [Attunity replikera Community](https://microsoft.attunity.com/) för en Snabbstartsguide, självstudier och support.
 - Detaljerade anvisningar om hur du använder Attunity replikera för att migrera din MySQL-databas till Azure-databas för MySQL finns i [databasen Migreringsguide](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).