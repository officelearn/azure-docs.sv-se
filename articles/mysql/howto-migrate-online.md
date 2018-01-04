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
ms.openlocfilehash: 429f5c9f939a802184a6513a63fb9115abf4b235
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2018
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Minimal avbrottstid migrering till Azure-databas för MySQL
Du kan migrera dina befintliga MySQL-databas till Azure-databas för MySQL med hjälp av Attunity replikera för Microsoft Migrations, ett samordna sponsrad, gemensamma erbjudande från Attunity och från Microsoft som har angetts tillsammans med tjänsten Azure Database migreringen utan extra kostnad för Microsoft-kunder. Attunity replikera för Microsoft Migrations kan också minsta avbrott i Databasmigrering, och källdatabasen fortsätter att köras under migreringsprocessen.

Attunity replikera är ett verktyg för replikering av data som möjliggör datasynkronisering mellan olika källor och mål, sprider schemat skapa skript och data som är associerade med varje databastabell. Attunity replikera inte spridas eventuella artefakter (till exempel SP utlösare, funktioner, etc.) eller konvertera, till exempel PL/SQL-kod som är värdbaserad i dessa artefakter att T-SQL.

> [!NOTE]
> Medan Attunity replikera stöder ett stort antal scenarier med migrering, fokuserar Attunity replikera för Microsoft Migrations på stöd för en delmängd av källan/målet par.

En översikt över processen för att utföra en migrering med minimal avbrottstid innehåller:

1. **Migrera MySQL datakällans schema** till en Azure-databas för MySQL-databas med hjälp av den [MySQL arbetsstationen](https://www.mysql.com/products/workbench/).

2. **Ställa in första last och kontinuerlig datasynkronisering från källdatabasen till måldatabasen** med hjälp av Attunity replikera för Microsoft Migrations. Detta minimerar den tid som källdatabasen måste anges som skrivskyddade när du förbereder att växla dina program mål MySQL-databas på Azure.

Mer information om Attunity replikera för Microsoft Migrations erbjudande visa följande resurser:
 - Attunity replikera för Microsoft Migrations [webbsida](https://aka.ms/attunity-replicate).
 - [Hämta](http://discover.attunity.com/download-replicate-microsoft-lp6657.html) Attunity replikera för migrering av Microsoft.
 - Attunity replikera [Community](https://microsoft.attunity.com/), med en Snabbstartsguide, självstudier och support.
 - Stegvisa instruktioner om hur du använder Attunity för att migrera från MySQL till Azure-databas för MySQL avser den [databasen Migreringsguide](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql).