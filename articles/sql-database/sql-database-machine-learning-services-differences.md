---
title: Viktiga skillnader vad gäller för Machine Learning Services (med R) översikt över Azure SQL Database (förhandsversion)
description: Det här avsnittet beskrivs viktiga skillnader mellan Azure SQL Database Machine Learning Services (med R) och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: adc303e9b375aaa6f37e9e79ea5434675c75523d
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824260"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Viktiga skillnader mellan Machine Learning-tjänster i Azure SQL Database och SQL Server

Funktionerna i Machine Learning Services (med R) i Azure SQL Database liknar [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan visas några huvudsakliga skillnader mellan dessa.

## <a name="language-support"></a>Stöd för språk

SQL Server har stöd för R och Python via den [utökningsbarhet framework](https://docs.microsoft.com/en-us/sql/advanced-analytics/concepts/extensibility-framework). SQL Database stöder inte båda språken. De viktigaste skillnaderna är:

- R är det enda språket som stöds i SQL-databas. Det finns inget stöd för Python just nu.
- R-versionen är 3.4.4.
- Behöver inte konfigurera `external scripts enabled` via `sp_configure`. När du är [registrerad](sql-database-machine-learning-services-overview.md#signup), machine learning-är aktiverad för SQL-databasen.

## <a name="package-management"></a>Pakethantering

R-pakethantering och installationen fungerar olika mellan SQL Database och SQL Server. Dessa skillnader är:

- R-paket installeras [sqlmlutils](https://github.com/Microsoft/sqlmlutils) eller [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Paket kan inte utföra utgående nätverksanrop. Den här begränsningen liknar den [standard brandväggsregler för Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) i SQL Server, men kan inte ändras i SQL-databas.
- Det finns inget stöd för paket som är beroende av externa körningar (till exempel Java) eller behöver åtkomst till OS APIs för installation och användning.

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa R-resurser via [Resursstyrningen](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) och externa resurspooler. R-resurser är en del av SQL Database-resurser, och beror på vilken tjänstenivå som du väljer. Mer information finns i [Azure SQL Database köpa modeller](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Säkerhetsisolering

I Azure SQL Database ger SQL plattform Abstraction Layer (SQLPAL) isolering för externa processer. Denna isolering ger ett extra säkerhetslager för att köra R-skript.

## <a name="next-steps"></a>Nästa steg

- Se den [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics) dokumentationen för allmän information
- Läs hur du använder Machine Learning-tjänster (med R) i Azure SQL Database i [Snabbstartsguide](sql-database-connect-query-r.md).
- Lär dig mer med [självstudier för SQL Server R-språk](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)