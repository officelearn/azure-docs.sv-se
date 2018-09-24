---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 70128735aef64d273c63236a13b0ae28edb6077d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003930"
---
### <a name="sql-databases"></a>SQL Databases

|  |  |
|---------|---------|
| [Tillåtna SKU:er för SQL DB](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Kräver att för SQL Database använder en godkänd SKU. Du kan ange en matris med tillåtna ID:n för SKU:er eller en matris med tillåtna SKU-namn. |
| [Granska inställningar för hotidentifiering på DB-nivå](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska för SQL Database-kryptering](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Granskar om transparent datakryptering inte är aktiverat för SQL Database. |
| [Granska granskningsinställning på SQL DB-nivå](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Granskar granskningsinställningar för SQL Database om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade.  |
| [Granska status för transparent datakryptering](../articles/governance/policy/samples/audit-trans-data-enc-status.md) | Granskar transparent datakryptering för SQL Database om det inte är aktiverat.  |