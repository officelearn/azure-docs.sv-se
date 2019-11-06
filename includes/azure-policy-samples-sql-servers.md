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
ms.openlocfilehash: dabadf3a4175947f09ba20b0e644f1d2b485ee38
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73591466"
---
### <a name="sql-servers"></a>SQL Servrar

|  |  |
|---------|---------|
| [Granska när det saknas Azure Active Directory-administratör](../articles/governance/policy/samples/audit-no-aad-admin.md) | Granska när det inte finns någon Azure Active Directory-administratör som tilldelats till SQL-servern. |
| [Granska inställningar för hotidentifiering på servernivå](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Granskar principer för SQL Database-säkerhetsaviseringar om principerna inte är inställda på det angivna tillståndet. Du kan ange ett värde som anger om hotidentifiering är aktiverat eller inaktiverat.  |
| [Granska granskningsinställningar för SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Granskar SQL Server beroende på om granskningsinställningarna är aktiverade. |
| [Granska granskningsinställningar på SQL Server-nivå](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Granskar granskningsinställningar för SQL Server om inställningarna inte matchar en angiven inställning. Du kan ange ett värde som anger om granskningsinställningar ska vara aktiverade eller inaktiverade. |