---
title: inkludera fil
description: inkludera fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 86d03e980a233a60e4f3a6541462264dae0862af
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "74005563"
---
Den här artikeln innehåller rikt linjer för att skapa högpresterande program med Azure Premium Storage. Du kan använda instruktionerna i det här dokumentet tillsammans med bästa metoder för prestanda som gäller för tekniker som används av ditt program. För att illustrera rikt linjerna har vi använt SQL Server som körs på Premium Storage som ett exempel i det här dokumentet.

Vi hanterar prestanda scenarier för lagrings skiktet i den här artikeln, men du måste optimera program skiktet. Om du till exempel är värd för en SharePoint-grupp på Azure Premium Storage kan du använda SQL Server-exemplen i den här artikeln för att optimera databas servern. Optimera dessutom SharePoint-servergruppens webb server och program Server för att få ut mesta möjliga prestanda.

Den här artikeln hjälper dig att besvara vanliga frågor om hur du optimerar program prestanda på Azure Premium Storage

* Hur mäter du program prestanda?  
* Varför ser du inte förväntad hög prestanda?  
* Vilka faktorer påverkar programmets prestanda på Premium Storage?  
* Hur påverkar dessa faktorer prestanda för ditt program på Premium Storage?  
* Hur kan du optimera för IOPS, bandbredd och latens?  

Vi har tillhandahållit dessa rikt linjer specifikt för Premium Storage eftersom arbets belastningar som körs på Premium Storage är mycket prestanda känsliga. Vi har fått exempel där det är lämpligt. Du kan också använda vissa av dessa rikt linjer för program som körs på virtuella IaaS-datorer med standard lagrings diskar.