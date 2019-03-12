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
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554133"
---
Följande begränsningar gäller för Azure Backup.

| **Gränsen** | **Standard** |
| --- | --- |
| Servrar eller datorer som kan registreras i ett valv. | Windows Server/Windows klient-/ System Center Data Protection Manager: 50. <br/><br/> Virtuella IaaS-datorer: 1,000.  |
| Storleken på en datakälla i valvet lagring. |Högst 54,400-GB. Gränsen gäller inte för IaaS VM-säkerhetskopiering. |
| Säkerhetskopieringsvalv i en Azure-prenumeration. |500 valv per region. |
| Schemalägg daglig säkerhetskopiering. |Windows Server/klient: Tre per dag.<br/> System Center DPM: Två per dag. <br/> Virtuella IaaS-datorer: En gång om dagen.  |
| Diskar som är anslutna till en Azure virtuell dator för säkerhetskopiering. | 16 |
| Enskilda datadisk som är ansluten till en Azure virtuell dator för säkerhetskopiering.| 4095 GB|
