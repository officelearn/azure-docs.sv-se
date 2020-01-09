---
title: Hot identifiering för data tjänster i Azure Security Center | Microsoft Docs
description: Den här artikeln visar de data Services-aviseringar som är tillgängliga i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 6d6e48c84f558840b3266193c4cbb9c3576f1a58
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665742"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Hot identifiering för data tjänster i Azure Security Center

 Azure Security Center analyserar loggarna för data lagrings tjänster och utlöser aviseringar när ett hot upptäcks för dina data resurser. Den här artikeln innehåller en lista över de aviseringar som Security Center genererar för följande tjänster:

* [Azure SQL Database och Azure SQL Data Warehouse](#data-sql)
* [Azure Storage](#azure-storage)
* [Azure Cosmos DB](#cosmos-db)

## SQL Database och SQL Data Warehouse<a name="data-sql"></a>

Avancerat skydd för Azure SQL Database identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Du ser aviseringar när det finns misstänkta databas aktiviteter, potentiella sårbarheter eller SQL-injektering, samt avvikande databas åtkomst och fråge mönster.

Avancerat skydd för Azure SQL Database och SQL är en del av det enhetliga paketet [Advanced Data Security (Ads)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) Unified för avancerade SQL-säkerhetsfunktioner, som omfattar Azure SQL-databaser, Azure SQL Database hanterade instanser, Azure SQL Data Warehouse databaser och SQL-servrar på Azure Virtual Machines.

Mer information finns här:

* [Så här aktiverar du Avancerat skydd för Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Så här aktiverar du Avancerat skydd för SQL-servrar på Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [Listan med aviseringar om skydd mot hot för SQL Database och SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)

## Azure Storage<a name="azure-storage"></a>

Avancerat skydd för lagring (som för närvarande endast är tillgängligt för Blob Storage) identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Det här skydds lagret gör att du kan åtgärda hot utan att du behöver vara säkerhets expert, och hjälper dig att hantera dina säkerhets övervaknings system.

>[!NOTE]
>Avancerat skydd för lagring är för närvarande inte tillgängligt i Azure myndigheter och suveräna moln regioner.

Mer information finns här:

* [Så här aktiverar du Avancerat skydd för Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Listan med aviseringar om skydd mot hot för Azure Storage](alerts-reference.md#alerts-azurestorage)

## Azure Cosmos DB<a name="cosmos-db"></a>

Azure Cosmos DB-aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton.

Mer information finns här:

* [Avancerat skydd för Azure Cosmos DB (för hands version)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Listan med aviseringar för skydd mot Azure Cosmos DB (för hands version)](alerts-reference.md#alerts-azurecosmos)
