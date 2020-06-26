---
title: Viktiga skillnader för Machine Learning Services (förhands granskning)
description: I det här avsnittet beskrivs viktiga skillnader mellan Azure SQL Database Machine Learning Services (med R) och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a8f5258c4b630c7d101db56587f677e3366dcd05
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413101"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Viktiga skillnader mellan Machine Learning Services i Azure SQL Database (för hands version) och SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Funktionerna i Azure SQL Database Machine Learning Services (med R) i (för hands version) liknar [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan visas några viktiga skillnader.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Stöd för språk

SQL Server har stöd för R och python genom [utöknings ramverket](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database stöder inte båda språken. De viktigaste skillnaderna är:

- R är det enda språk som stöds i SQL Database. Det finns inget stöd för Python just nu.
- R-versionen är 3.4.4.
- Du behöver inte konfigurera `external scripts enabled` via `sp_configure` .

## <a name="package-management"></a>Pakethantering

Hantering av R-paket och installations arbete skiljer sig mellan SQL Database och SQL Server. Dessa skillnader är:

- R-paket installeras via [sqlmlutils](https://github.com/Microsoft/sqlmlutils) eller [skapa externa bibliotek](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Paket kan inte utföra utgående nätverks anrop. Den här begränsningen liknar [standard brand Väggs reglerna för Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) i SQL Server, men kan inte ändras i SQL Database.
- Det finns inget stöd för paket som är beroende av externa körningar (t. ex. Java) eller som behöver åtkomst till OS-API: er för installation eller användning.

## <a name="writing-to-a-temporary-table"></a>Skriver till en temporär tabell

Om du använder RODBC i Azure SQL Database kan du inte skriva till en temporär tabell, oavsett om den har skapats i eller utanför `sp_execute_external_script` sessionen. Lösningen är att använda [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) och [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (med överskrivning = falskt och append = "rader") för att skriva till en global temporär tabell som skapats före `sp_execute_external_script` frågan.

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa R-resurser via [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) och externa resurspooler.

Under den offentliga för hands versionen har R-resurser högst 20% av SQL Database resurser och är beroende av vilken tjänst nivå du väljer. Mer information finns i [Azure SQL Database inköps modeller](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Otillräckligt minnes fel

Om det inte finns tillräckligt med minne tillgängligt för R får du ett fel meddelande. Vanliga fel meddelanden är:

- Det gick inte att kommunicera med körningen för R-skriptet för begärande-ID: * * * * * * *. Kontrol lera kraven för "R"-körningen
- Ett R-skript fel inträffade vid körning av sp_execute_external_script med HRESULT-0x80004004. ... ett externt skript fel har inträffat: ".. Det gick inte att allokera minne (0 MB) i C-funktionen R_AllocStringBuffer
- Ett externt skript fel har inträffat: fel: det går inte att allokera storleks vektor.

Minnes användningen beror på hur mycket som används i R-skripten och antalet parallella frågor som körs. Om du får felen ovan kan du skala databasen till en högre tjänst nivå för att lösa detta.

## <a name="next-steps"></a>Nästa steg

- Se översikten [Azure SQL Database Machine Learning Services med R (för hands version)](machine-learning-services-overview.md).
- Information om hur du använder R för att fråga Azure SQL Database Machine Learning Services (förhands granskning) finns i [snabb starts guiden](connect-query-r.md).
- För att komma igång med vissa enkla R-skript, se [skapa och köra enkla r-skript i Azure SQL Database Machine Learning Services (för hands version)](r-script-create-quickstart.md).
