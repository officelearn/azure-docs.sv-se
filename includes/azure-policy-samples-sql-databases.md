---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 50a6388a95bce14bc9af7e0c9a5387e148f6fa73
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664713"
---
### <a name="sql-databases"></a>SQL Databases

|  |  |
|---------|---------|
| [Tillåtna SKU:er för SQL DB](../articles/azure-policy/scripts/allowed-sql-db-skus.md) | Kräver att för SQL Database använder en godkänd SKU. Du kan ange en matris med tillåtna ID:n för SKU:er eller en matris med tillåtna SKU-namn. |
| [Granska inställningar för hotidentifiering på DB-nivå](../articles/azure-policy/scripts/audit-db-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska för SQL Database-kryptering](../articles/azure-policy/scripts/sql-database-encryption-audit.md) | Granskar om transparent datakryptering inte är aktiverat för SQL Database. |
| [Granska granskningsinställning på SQL DB-nivå](../articles/azure-policy/scripts/audit-sql-db-audit-setting.md) | Granskar granskningsinställningar för SQL Database om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade.  |
| [Granska status för transparent datakryptering](../articles/azure-policy/scripts/audit-trans-data-enc-status.md) | Granskar transparent datakryptering för SQL Database om det inte är aktiverat.  |