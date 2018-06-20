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
ms.openlocfilehash: 298b92205e73b3623db02fb1dd803edac8379700
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34664703"
---
### <a name="sql-servers"></a>SQL Servrar

|  |  |
|---------|---------|
| [Granska när det saknas Azure Active Directory-administratör](../articles/azure-policy/scripts/audit-no-aad-admin.md) | Granska när det inte finns någon Azure Active Directory-administratör som tilldelats till SQL-servern. |
| [Granska inställningar för hotidentifiering på servernivå](../articles/azure-policy/scripts/audit-sql-ser-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska granskningsinställningar för SQL Server](../articles/azure-policy/scripts/sql-server-audit.md) | Granskar SQL Server beroende på om granskningsinställningarna är aktiverade. |
| [Granska granskningsinställningar på SQL Server-nivå](../articles/azure-policy/scripts/audit-sql-ser-leve-audit-setting.md) | Granskar granskningsinställningar för SQL Server om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade. |
| [Kräv SQL Server version 12.0](../articles/azure-policy/scripts/req-sql-12.md) | Kräver att SQL-servrar använder version 12.0.  |