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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2019
ms.locfileid: "67187743"
---
Följande begränsningar gäller för Azure Backup.

| **Gränsen** | **Standard** |
| --- | --- |
| Servrar eller datorer som kan registreras i ett valv. | Windows Server/Windows-klient/System Center Data Protection Manager: 50. <br/><br/> Virtuella IaaS-datorer: 1 000.  |
| Storlek på en data källa i valv lagringen. |54 400 – högst-GB. Gränsen gäller inte för IaaS VM-säkerhetskopiering. |
| Säkerhetskopiera valv i en Azure-prenumeration. |500-valv per region. |
| Schemalägg dagliga säkerhets kopieringar. |Windows Server/klient: Tre dagar.<br/> System Center DPM: Två dagar. <br/> Virtuella IaaS-datorer: En gång om dagen.  |
| Data diskar som är anslutna till en virtuell Azure-dator för säkerhets kopiering. | 16 |
| Enskild datadisk som är ansluten till den virtuella Azure-datorn för säkerhets kopiering.| 4 095 GB|
