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
ms.openlocfilehash: 1f41567f7d5dcc9b6536b47ef639ba53abff8ec4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756018"
---
Följande begränsningar gäller för Azure Backup.

| Gräns för identifierare | Standardgräns |
| --- | --- |
| Många servrar/datorer som kan registreras mot varje valv |50 för Windows Server/klient-/ SCDPM <br/> 1000 för virtuella IaaS-datorer |
| Storleken på en datakälla för data som lagras i Azure vault lagring |54400 GB maximal<sup>1</sup> |
| Antalet säkerhetskopieringsvalv som kan skapas i varje Azure-prenumeration |500 recovery Services-valv per region |
| Antal gånger som säkerhetskopiering kan schemaläggas per dag |3 per dag för Windows Server/klient <br/> 2 per dag för SCDPM <br/> En gång per dag för virtuella IaaS-datorer |
| Diskar som är anslutna till en Azure-dator för säkerhetskopiering |16 |
| Storleken på enskilda datadisk som är ansluten till en Azure-dator för säkerhetskopiering| 4 095 GB <sup>2</sup>|

* <sup>1</sup>54400 GB gränsen gäller inte för IaaS VM-säkerhetskopiering.
 

