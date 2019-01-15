---
title: ta med fil
description: ta med fil
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9ad1208283527f35e04dede2706fd0e424096dc5
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54300617"
---
Följande begränsningar gäller för Azure Backup.

| **Gränsen** | **Standard** |
| --- | --- |
| Servrar/datorer som kan registreras i ett valv | Windows Server/Windows klient-/ System Center DPM: 50 <br/><br/> Virtuella IaaS-datorer: 1000  |
| Storleken på en datakälla i valvet storage |Högst 54400 GB. Gränsen gäller inte för IaaS VM-säkerhetskopiering |
| Säkerhetskopieringsvalv i en Azure-prenumeration |500 valv per region |
| Schemalägg daglig säkerhetskopiering |Windows Server/klient: 3 per dag<br/> System Center DPM: 2 per dag <br/> Virtuella IaaS-datorer: En gång om dagen  |
| Diskar som är anslutna till en Azure virtuell dator för säkerhetskopiering | 16 |
| Enskilda datadisk som är ansluten till en Azure virtuell dator för säkerhetskopiering| 4 095 GB|
