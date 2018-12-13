---
title: ta med fil
description: ta med fil
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318359"
---
### <a name="sql-databases"></a>SQL Databases

|  |  |
|---------|---------|
| [Tillåtna SKU:er för SQL DB](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Kräver att för SQL Database använder en godkänd SKU. Du kan ange en matris med tillåtna ID:n för SKU:er eller en matris med tillåtna SKU-namn. |
| [Granska inställningar för hotidentifiering på DB-nivå](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska för SQL Database-kryptering](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Granskar om transparent datakryptering inte är aktiverat för SQL Database. |
| [Granska granskningsinställning på SQL DB-nivå](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Granskar granskningsinställningar för SQL Database om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade.  |