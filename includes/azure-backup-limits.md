---
title: ta med fil
description: ta med fil
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 2/7/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: b345283f87c446ff3b583b0c5dd8a4be222303ae
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
Följande begränsningar gäller för Azure Backup.

| Gränsen identifierare | Standardgräns |
| --- | --- |
| Många servrar/datorer som kan registreras mot varje valvet |50 för Windows Server/Client/SCDPM <br/> 200 för IaaS-VM |
| Storleken på en datakälla för data som lagras i Azure valvet lagring |54400 GB max<sup>1</sup> |
| Antal säkerhetskopieringsvalv som kan skapas i varje Azure-prenumeration |25 recovery Services-valv per region |
| Antal gånger som säkerhetskopiering kan schemaläggas per dag |3 per dag för Windows Server eller-klienten <br/> 2 per dag för SCDPM <br/> En gång om dagen för IaaS-VM |
| Datadiskar som är anslutna till en virtuell Azure-dator för säkerhetskopiering |16 |
| Storleken på enskilda disk ansluten till en virtuell Azure-dator för säkerhetskopiering| 4095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB gränsen gäller inte för IaaS VM-säkerhetskopiering.
 

