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
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717134"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure premium storage: design för hög prestanda

Den här artikeln innehåller riktlinjer för att skapa program med hög prestanda med hjälp av Azure Premium Storage. Du kan använda instruktionerna i det här dokumentet som kombineras med bästa praxis för prestanda gäller för tekniker som används av ditt program. För att visa riktlinjerna kan använda vi SQL Server på Premium Storage som ett exempel i hela dokumentet.

Medan vi bemöter prestanda scenarier för Storage-skiktet i den här artikeln behöver du optimera programnivån. Om du är värd för en SharePoint-servergrupp i Azure Premium Storage, kan du till exempel använda SQL Server-exempel från den här artikeln för att optimera databasservern. Dessutom kan optimera SharePoint-servergruppen webbserver och programserver att få de flesta prestanda.

Den här artikeln kommer att besvara följande vanliga frågor om hur du optimerar programprestanda på Azure Premium Storage

* Så här att mäta programprestanda?  
* Varför inte ser du hög prestanda?  
* Vilka faktorer påverkar programprestanda på Premium Storage?  
* Hur dessa faktorer påverkar prestanda för ditt program i Premium Storage?  
* Hur kan du optimera för IOPS, bandbredd och latens?  

Vi har angett dessa riktlinjer specifikt för Premium Storage eftersom arbetsbelastningar som körs på Premium-lagring med hög prestanda som är känsliga. Vi har lagt till exempel där det är lämpligt. Du kan också använda några av dessa riktlinjer för program som körs på virtuella IaaS-datorer med Standard Storage-diskar.