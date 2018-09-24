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
ms.openlocfilehash: 76e161be1adca4aa2ec7b682a13b0a42e4b79412
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003911"
---
### <a name="sql-servers"></a>SQL Servrar

|  |  |
|---------|---------|
| [Granska när det saknas Azure Active Directory-administratör](../articles/governance/policy/samples/audit-no-aad-admin.md) | Granska när det inte finns någon Azure Active Directory-administratör som tilldelats till SQL-servern. |
| [Granska inställningar för hotidentifiering på servernivå](../articles/governance/policy/samples/audit-sql-ser-threat-det-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska granskningsinställningar för SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Granskar SQL Server beroende på om granskningsinställningarna är aktiverade. |
| [Granska granskningsinställningar på SQL Server-nivå](../articles/governance/policy/samples/audit-sql-ser-leve-audit-setting.md) | Granskar granskningsinställningar för SQL Server om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade. |
| [Kräv SQL Server version 12.0](../articles/governance/policy/samples/req-sql-12.md) | Kräver att SQL-servrar använder version 12.0.  |