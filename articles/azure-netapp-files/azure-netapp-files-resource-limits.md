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
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452644"
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

[Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
