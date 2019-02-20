---
title: Resursgränser för Azure NetApp Files | Microsoft Docs
description: Beskriver begränsningarna för Azure NetApp Files-resurser, till exempel begränsningar för NetApp-konton, kapacitet pooler, volymer, ögonblicksbilder och delegerad undernätet.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: concepts
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 196d85917e0a9900e141d58bff171beeb8540409
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430021"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Så här fungerar resursgränser för Azure NetApp Files kan du hantera dina volymer.

- Varje Azure-prenumeration kan ha högst 10 NetApp-konton.
- Varje NetApp-konto kan ha högst 25 kapacitet pooler.
- Varje kapacitetspool kan endast tillhör ett NetApp-konto.  
- Den minsta storleken för en enda kapacitetspool är 4 TiB och maxstorleken är 500 TiB. 
- Varje kapacitet pool kan ha högst 500 volymer.
- Den minsta storleken är 100 TiB och maxstorleken är 92 TiB.
- Varje volym kan ha högst 255 ögonblicksbilder.
- Varje virtuellt Azure-nätverk (Vnet) kan ha endast ett undernät som delegerats till Azure NetApp-filer.

**Nästa steg**

[Förstå Azure NetApp Files storage hierarki](azure-netapp-files-understand-storage-hierarchy.md)
