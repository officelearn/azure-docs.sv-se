---
title: Viktiga skillnader för Machine Learning Services (förhands granskning)
description: I den här artikeln beskrivs viktiga skillnader mellan Machine Learning Services i Azure SQL-hanterad instans och SQL Server Machine Learning Services.
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
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324541"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Viktiga skillnader medllan Machine Learning Services i Azure SQL Managed Instance och SQL Server

Funktionerna i [Machine Learning Services i Azure SQL-hanterad instans (för hands version)](machine-learning-services-overview.md) är nästan identiska med [SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning). Nedan följer några viktiga skillnader.

> [!IMPORTANT]
> Machine Learning Services i Azure SQL Managed instance är för närvarande en offentlig för hands version. För att registrera dig, se [Registrera dig för för hands versionen](machine-learning-services-overview.md#signup).

## <a name="preview-limitations"></a>Begränsningar för förhandsversion

Tjänsten har följande begränsningar i förhandsversionen:

- Loopback-anslutningar fungerar inte (se [loopback-anslutning till SQL Server från ett python-eller R-skript](/sql/machine-learning/connect/loopback-connection)).
- Externa resurspooler stöds inte.
- Endast Python och R stöds. Det går inte att lägga till externa språk som Java.
- Scenarier som använder MPI ( [Message Passing Interface](/message-passing-interface/microsoft-mpi) ) stöds inte.

Om du uppdaterar service nivå mål (service nivå mål) uppdaterar du service nivå mål och höjer ett support ärende för att återaktivera de dedikerade resurs gränserna för R/python.

## <a name="language-support"></a>Stöd för språk

Machine Learning Services i SQL-hanterad instans och SQL Server stöd för både python-och R- [utöknings ramverket](/sql/advanced-analytics/concepts/extensibility-framework). De viktigaste skillnaderna är:

- De ursprungliga versionerna av python och R skiljer sig mellan Machine Learning Services i SQL-hanterad instans och SQL Server:

  | System               | Python | R     |
  |----------------------|--------|-------|
  | SQL-hanterad instans | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- Du behöver inte konfigurera `external scripts enabled` via `sp_configure` . När du har [registrerat](machine-learning-services-overview.md#signup) dig för för hands versionen är Machine Learning aktiverat för Azure SQL-hanterad instans.

## <a name="packages"></a>Paket

Hantering av python-och R-paket fungerar annorlunda mellan SQL-hanterad instans och SQL Server. Dessa skillnader är:

- Det finns inget stöd för paket som är beroende av externa körningar (t. ex. Java) eller som behöver åtkomst till OS-API: er för installation eller användning.
- Paket kan utföra utgående nätverks anrop (ändras från tidigare i förhands granskningen). Du kan ställa in rätt utgående säkerhets regler på nivån [nätverks säkerhets grupp](../../virtual-network/network-security-groups-overview.md) för att aktivera utgående nätverks anrop.

Mer information om hur du hanterar python-och R-paket finns i:

- [Hämta information om Python-paket](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [Hämta information om R-paket](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>Resursstyrning

Det går inte att begränsa R-resurser via [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) och externa resurspooler.

Under den allmänt tillgängliga förhandsversionen är R-resurserna inställda på maximalt 20 % av de SQL-hanterade instansresurserna och är beroende av vilken tjänstnivå du väljer. Mer information finns i [Azure SQL Database inköps modeller](../database/purchasing-models.md).

### <a name="insufficient-memory-error"></a>Otillräckligt minnes fel

Om det inte finns tillräckligt med tillgängligt minne för R får du ett felmeddelande. Vanliga fel meddelanden är:

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

Minnesanvändningen beror på hur mycket som används i R-skripten och hur många frågor som körs parallellt. Om du får felen ovan kan du lösa detta genom att skala databasen till en högre tjänstnivå.

## <a name="next-steps"></a>Nästa steg

- Se översikten [Machine Learning Services i Azure SQL-hanterad instans](machine-learning-services-overview.md).
- Information om hur du använder python i Machine Learning Services finns i [köra Python-skript](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Information om hur du använder R i Machine Learning Services finns i [Kör R-skript](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).