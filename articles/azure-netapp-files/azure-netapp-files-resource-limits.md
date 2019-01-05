---
title: Resursgränser för Azure NetApp Files | Microsoft Docs
description: Beskriver begränsningarna för Azure NetApp Files-resurser, till exempel begränsningar för kapacitet pooler, volymer och delegerad undernätet.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54057161"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursgränser för Azure NetApp-filer
Så här fungerar resursgränser för Azure NetApp Files kan du hantera dina volymer.

## <a name="capacity_pools"></a>Kapacitetspooler

- Den minsta storleken för en enda kapacitetspool är 4 TiB och maxstorleken är 500 TiB. 
- Varje kapacitetspool kan endast tillhör ett NetApp-konto. Du kan dock ha flera kapacitetspooler inom ett NetApp-konto.  

## <a name="volumes"></a>Volymer

- Den minsta storleken är 100 TiB och maxstorleken är 92 TiB.
- Du kan ha högst 100 volymer per Azure-prenumeration per region.  

## <a name="delegated_subnet"></a>Delegerad undernät 

Endast ett undernät kan delegeras till Azure NetApp-filer i varje Azure virtuellt nätverk (Vnet).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure NetApp Files storage hierarki](azure-netapp-files-understand-storage-hierarchy.md)
