---
title: Viktigaste skillnaderna för Azure SQL Database Machine Learning Services (förhandsversion)
description: Det här avsnittet beskrivs viktiga skillnader mellan Azure SQL Database Machine Learning Services (med R) och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 57ea52c179376e8378680f436d396ffaf9357f68
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771858"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Viktiga skillnader mellan Machine Learning-tjänster i Azure SQL Database (förhandsversion) och SQL Server

Funktionerna i Azure SQL Database Machine Learning Services (med R) (förhandsversion) liknar [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan visas några huvudsakliga skillnader.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Stöd för språk

SQL Server har stöd för R och Python via den [utökningsbarhet framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database stöder inte båda språken. De viktigaste skillnaderna är:

- R är det enda språket som stöds i SQL-databas. Det finns inget stöd för Python just nu.
- R-versionen är 3.4.4.
- Behöver inte konfigurera `external scripts enabled` via `sp_configure`. När du är [registrerad](sql-database-machine-learning-services-overview.md#signup), machine learning-är aktiverad för SQL-databasen.

## <a name="package-management"></a>Pakethantering

R-pakethantering och installationen fungerar olika mellan SQL Database och SQL Server. Dessa skillnader är:

- R-paket installeras [sqlmlutils](https://github.com/Microsoft/sqlmlutils) eller [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Paket kan inte utföra utgående nätverksanrop. Den här begränsningen liknar den [standard brandväggsregler för Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) i SQL Server, men kan inte ändras i SQL-databas.
- Det finns inget stöd för paket som är beroende av externa körningar (till exempel Java) eller behöver åtkomst till OS APIs för installation och användning.

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa R-resurser via [Resursstyrningen](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) och externa resurspooler.

Den offentliga förhandsversionen, R-resurser är inställda på högst 20% av SQL Database-resurser, och beror på vilken tjänstenivå som du väljer. Mer information finns i [Azure SQL Database köpa modeller](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Otillräckligt med minne-fel

Om det finns inte tillräckligt med minne för R, får du ett felmeddelande. Vanliga felmeddelanden är:

- Det går inte att kommunicera med körningen av skriptet ”R” för begärande-id: ***. Kontrollera kraven för ”R” runtime
- ”R”-skriptfel inträffade vid körning av ”sp_execute_external_script” med HRESULT 0x80004004. ... en externt skriptfel inträffade ”:... Det gick inte att allokera minne (0 Mb) i C-funktionen ”R_AllocStringBuffer” ”
- Det uppstod ett externt skriptfel: Fel: Det går inte att allokera vektor storlek.

Användningen beror på hur mycket minne används i R-skript och antalet parallella frågor som körs. Om du får fel ovan, kan du skala databasen till en högre tjänstnivå för att lösa problemet.

## <a name="next-steps"></a>Nästa steg

- Se den [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) dokumentationen för allmän information
- Läs hur du använder Machine Learning-tjänster (med R) i Azure SQL Database i [Snabbstartsguide](sql-database-connect-query-r.md).
- Lär dig mer med [självstudier för SQL Server R-språk](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)