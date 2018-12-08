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
ms.openlocfilehash: 63922eb623576379058c9a8a367d6e52249115f2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109101"
---
Följande begränsningar gäller för Azure Backup.

| **Gränsen** | **Standard** |
| --- | --- |
| Servrar/datorer som kan registreras i ett valv | Windows Server/Windows klient-/ System Center DPM: 50 <br/><br/> Virtuella IaaS-datorer: 1000  |
| Storleken på en datakälla i valvet storage |Högst 54400 GB. Gränsen gäller inte för IaaS VM-säkerhetskopiering |
| Säkerhetskopieringsvalv i en Azure-prenumeration |500 valv per region |
| Schemalägg daglig säkerhetskopiering |Windows Server/klient: 3 per dag<br/> System Center DPM: 2 per dag <br/> IaaS-VM: En gång om dagen  |
| Diskar som är anslutna till en Azure virtuell dator för säkerhetskopiering | 32 |
| Enskilda datadisk som är ansluten till en Azure virtuell dator för säkerhetskopiering| 4 095 GB|



