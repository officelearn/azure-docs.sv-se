---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74005563"
---
Den här artikeln innehåller riktlinjer för att skapa högpresterande program med Azure Premium Storage. Du kan använda instruktionerna i det här dokumentet i kombination med metodtips för prestanda som gäller för tekniker som används av ditt program. För att illustrera riktlinjerna har vi använt SQL Server som körs på Premium Storage som ett exempel i hela det här dokumentet.

Även om vi tar itu med prestandascenarier för lagringslagret i den här artikeln måste du optimera programlagret. Om du till exempel är värd för en SharePoint-servergrupp på Azure Premium Storage kan du använda SQL Server-exemplen från den här artikeln för att optimera databasservern. Optimera dessutom SharePoint-gruppens webbserver och programserver för att få ut mesta av prestanda.

Den här artikeln hjälper dig att svara på följande vanliga frågor om hur du optimerar programprestanda på Azure Premium Storage,

* Hur mäter du programmets prestanda?  
* Varför ser du inte förväntad hög prestanda?  
* Vilka faktorer påverkar din applikationsprestanda på Premium Storage?  
* Hur påverkar dessa faktorer programmets prestanda på Premium Storage?  
* Hur kan du optimera för IOPS, bandbredd och latens?  

Vi har tillhandahållit dessa riktlinjer specifikt för Premium Storage eftersom arbetsbelastningar som körs på Premium Storage är mycket prestandakänsliga. Vi har gett exempel där så är lämpligt. Du kan också använda vissa av dessa riktlinjer för program som körs på virtuella IaaS-datorer med standardlagringsdiskar.