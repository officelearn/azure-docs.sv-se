---
title: Viktiga skillnader för Machine Learning Services (förhands granskning)
description: I det här avsnittet beskrivs viktiga skillnader mellan Machine Learning Services i Azure SQL-hanterad instans och SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: 9ff2de18042c466bdd8fa6c71194fff4286c820d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325104"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Viktiga skillnader medllan Machine Learning Services i Azure SQL Managed Instance och SQL Server

Funktionerna i [Machine Learning Services i Azure SQL-hanterad instans (för hands version)](machine-learning-services-overview.md) är nästan identiska med [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan följer några viktiga skillnader.

> [!IMPORTANT]
> Machine Learning Services i Azure SQL Managed instance är för närvarande en offentlig för hands version. För att registrera dig, se [Registrera dig för för hands versionen](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Tjänsten har följande begränsningar i förhandsversionen:

- Loopback-anslutningar fungerar inte (se [loopback-anslutning till SQL Server från ett python-eller R-skript](/sql/machine-learning/connect/loopback-connection)).
- Externa resurspooler stöds inte.
- Endast Python och R stöds. Det går inte att lägga till externa språk som Java.
- Scenarier som använder MPI ( [Message Passing Interface](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) ) stöds inte.

Om det är en SLO-uppdatering (servicenivåmål) uppdaterar du SLO och skickar en supportbegäran för att återaktivera de dedikerade resursgränserna för R/Python.

## <a name="language-support"></a>Stöd för språk

Machine Learning Services i SQL-hanterad instans och SQL Server stöd för både python-och R- [utöknings ramverket](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). De viktigaste skillnaderna är:

- De ursprungliga versionerna av python och R skiljer sig mellan Machine Learning Services i SQL-hanterad instans och SQL Server:

  | System               | Python | R     |
  |----------------------|--------|-------|
  | SQL-hanterad instans | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Du behöver inte konfigurera `external scripts enabled` via `sp_configure` . När du har [registrerat](machine-learning-services-overview.md#signup) dig för för hands versionen är Machine Learning aktiverat för Azure SQL-hanterad instans.

## <a name="packages"></a>Paket

Hantering av python-och R-paket fungerar annorlunda mellan SQL-hanterad instans och SQL Server. Dessa skillnader är:

- Paket kan inte utföra utgående nätverks anrop. Den här begränsningen liknar [standard brand Väggs reglerna för Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) i SQL Server, men kan inte ändras i SQL-hanterad instans.
- Det finns inget stöd för paket som är beroende av externa körningar (t. ex. Java) eller som behöver åtkomst till OS-API: er för installation eller användning.

Mer information om hur du hanterar python-och R-paket finns i:

- [Hämta information om Python-paket](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [Hämta information om R-paket](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa R-resurser via [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) och externa resurspooler.

Under den allmänt tillgängliga förhandsversionen är R-resurserna inställda på maximalt 20 % av de SQL-hanterade instansresurserna och är beroende av vilken tjänstnivå du väljer. Mer information finns i [Azure SQL Database inköps modeller](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Otillräckligt minnes fel

Om det inte finns tillräckligt med tillgängligt minne för R får du ett felmeddelande. Vanliga fel meddelanden är:

- Det gick inte att kommunicera med körningen för R-skriptet för begärande-ID: * * * * * * *. Kontrollera kraven för R-runtime
- Ett R-skriptfel inträffade vid körning av ”sp_execute_external_script” med HRESULT 0x80004004. ... det uppstod ett externt skriptfel: "... det gick inte att allokera minne (0 MB) i C-funktionen ”R_AllocStringBuffer”
- Ett externt skript fel har inträffat: fel: det går inte att allokera storleks vektor.

Minnesanvändningen beror på hur mycket som används i R-skripten och hur många frågor som körs parallellt. Om du får felen ovan kan du lösa detta genom att skala databasen till en högre tjänstnivå.

## <a name="next-steps"></a>Nästa steg

- Se översikten [Machine Learning Services i Azure SQL-hanterad instans](machine-learning-services-overview.md).
- Information om hur du använder python i Machine Learning Services finns i [köra Python-skript](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
- Information om hur du använder R i Machine Learning Services finns i [Kör R-skript](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15).
