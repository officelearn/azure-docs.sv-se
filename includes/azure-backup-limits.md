---
title: ta med fil
description: ta med fil
services: backup
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: ca6c57b139b195b94dc7171336cfcc34d18b1872
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828823"
---
Följande begränsningar gäller för Azure Backup.

| **Gräns** | **Standard** |
| --- | --- |
| Servrar eller datorer som kan registreras i ett valv. | Windows Server/Windows Client/System Center Data Protection Manager: 50. <br/><br/> Virtuella IaaS-datorer: 1 000.  |
| Storlek på en data källa i valv lagringen. |54 400 – högst-GB. Gränsen gäller inte för IaaS VM-säkerhetskopiering. |
| Säkerhetskopiera valv i en Azure-prenumeration. |500-valv per region. |
| Schemalägg dagliga säkerhets kopieringar. |Windows Server/klient: tre dagar.<br/> System Center DPM: två dagar. <br/> Virtuella IaaS-datorer: en gång om dagen.  |
| Data diskar som är anslutna till en virtuell Azure-dator för säkerhets kopiering. | 16 |
| Enskild datadisk som är ansluten till den virtuella Azure-datorn för säkerhets kopiering.| 32 TB|
