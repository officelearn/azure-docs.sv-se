---
title: ta med fil
description: ta med fil
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 9/10/2018
ms.author: trinadhk;sogup
ms.custom: include file
ms.openlocfilehash: 64101ea5a3bbaac4a6b2e349a04d06ea84a87081
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44381108"
---
Följande begränsningar gäller för Azure Backup.

| Gräns för identifierare | Standardgräns |
| --- | --- |
| Många servrar/datorer som kan registreras mot varje valv |50 för Windows Server/klient-/ SCDPM <br/> 1000 för virtuella IaaS-datorer |
| Storleken på en datakälla för data som lagras i Azure vault lagring |54400 GB maximal<sup>1</sup> |
| Antalet säkerhetskopieringsvalv som kan skapas i varje Azure-prenumeration |500 recovery Services-valv per region |
| Antal gånger som säkerhetskopiering kan schemaläggas per dag |3 per dag för Windows Server/klient <br/> 2 per dag för SCDPM <br/> En gång per dag för virtuella IaaS-datorer |
| Diskar som är anslutna till en Azure-dator för säkerhetskopiering |16 |
| Storleken på enskilda datadisk som är ansluten till en Azure-dator för säkerhetskopiering| 4 095 GB|

* <sup>1</sup>54400 GB gränsen gäller inte för IaaS VM-säkerhetskopiering.
 

