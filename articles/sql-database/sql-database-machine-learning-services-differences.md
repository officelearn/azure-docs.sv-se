---
title: Viktiga skillnader för Machine Learning Services (förhandsgranskning)
description: I det här avsnittet beskrivs viktiga skillnader mellan Azure SQL Database Machine Learning Services (med R) och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453157"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Viktiga skillnader mellan Machine Learning Services i Azure SQL Database (förhandsversion) och SQL Server

Funktionerna i Azure SQL Database Machine Learning Services (med R) i (förhandsversion) liknar [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan följer några viktiga skillnader.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Stöd för språk

SQL Server har stöd för R och Python via [utökningsbarhetsramverket](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database stöder inte båda språken. De viktigaste skillnaderna är:

- R är det enda språk som stöds i SQL Database. Det finns inget stöd för Python just nu.
- R-versionen är 3.4.4.
- Det finns ingen `external scripts enabled` anledning `sp_configure`att konfigurera via .

## <a name="package-management"></a>Pakethantering

R pakethantering och installation fungerar olika mellan SQL Database och SQL Server. Dessa skillnader är:

- R-paket installeras via [sqlmlutils](https://github.com/Microsoft/sqlmlutils) eller [SKAPA EXTERNT BIBLIOTEK](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Paket kan inte utföra utgående nätverksanrop. Den här begränsningen liknar [standardbrandväggsreglerna för Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) i SQL Server, men kan inte ändras i SQL Database.
- Det finns inget stöd för paket som är beroende av externa körningar (som Java) eller behöver åtkomst till API:er för operativsystemet för installation eller användning.

## <a name="writing-to-a-temporary-table"></a>Skriva till en tillfällig tabell

Om du använder RODBC i Azure SQL Database kan du inte skriva till en tillfällig tabell, `sp_execute_external_script` oavsett om den skapas inom eller utanför sessionen. Lösningen är att använda [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) och [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (med overwrite=FALSE och append="rader") för att `sp_execute_external_script` skriva till en global temporär tabell som skapats före frågan.

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa [R-resurser](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) via resurschef och externa resurspooler.

Under den offentliga förhandsversionen anges R-resurser till högst 20 % av SQL Database-resurserna och beror på vilken tjänstnivå du väljer. Mer information finns i [Azure SQL Database inköpsmodeller](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Otillräckligt minnesfel

Om det inte finns tillräckligt med ledigt minne för R får du ett felmeddelande. Vanliga felmeddelanden är:

- Det går inte att kommunicera med körningen för R-skriptet för begärande-ID: *******. Kontrollera kraven för "R" runtime
- Skriptfelet för R inträffade under körningen av "sp_execute_external_script" med HRESULT 0x80004004. ... ett externt skriptfel uppstod: ".. kunde inte allokera minne (0 Mb) i C-funktionen "R_AllocStringBuffer""
- Ett externt skriptfel uppstod: Fel: det går inte att allokera vektor av storlek.

Minnesanvändningen beror på hur mycket som används i R-skripten och antalet parallella frågor som körs. Om du får felen ovan kan du skala databasen till en högre tjänstnivå för att lösa detta.

## <a name="next-steps"></a>Nästa steg

- Se översikten [Azure SQL Database Machine Learning Services med R (förhandsversion)](sql-database-machine-learning-services-overview.md).
- Mer information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhandsversion) finns i [snabbstartsguiden](sql-database-connect-query-r.md).
- Information om hur du kommer igång med några enkla R-skript finns i [Skapa och köra enkla R-skript i Azure SQL Database Machine Learning Services (förhandsversion).](sql-database-quickstart-r-create-script.md)
