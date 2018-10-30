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
ms.openlocfilehash: 314f29a1135e355597e890b72ef3c0b7372194e6
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226409"
---
### <a name="sql-databases"></a>SQL Databases

|  |  |
|---------|---------|
| [Tillåtna SKU:er för SQL DB](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Kräver att för SQL Database använder en godkänd SKU. Du kan ange en matris med tillåtna ID:n för SKU:er eller en matris med tillåtna SKU-namn. |
| [Granska inställningar för hotidentifiering på DB-nivå](../articles/governance/policy/samples/audit-db-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska för SQL Database-kryptering](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Granskar om transparent datakryptering inte är aktiverat för SQL Database. |
| [Granska granskningsinställning på SQL DB-nivå](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Granskar granskningsinställningar för SQL Database om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade.  |