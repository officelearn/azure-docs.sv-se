---
title: Resurs gränser för Azure NetApp Files | Microsoft Docs
description: Beskriver gränser för Azure NetApp Files resurser, inklusive gränser för NetApp-konton, kapacitets grupper, volymer, ögonblicks bilder och det delegerade under nätet.
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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: fe8d46c897ef68563f2e0e5a1da106174ae504c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424087"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Att förstå resurs gränser för Azure NetApp Files hjälper dig att hantera dina volymer.

## <a name="resource-limits"></a>Resursbegränsningar

I följande tabell beskrivs resurs gränser för Azure NetApp Files:

|  Resource  |  Standardgräns  |  Justerbar via supportbegäran  |
|----------------|---------------------|--------------------------------------|
|  Antal NetApp-konton per Azure-prenumeration   |  10    |  Ja   |
|  Antal kapacitets pooler per NetApp-konto   |    25     |   Ja   |
|  Antal volymer per kapacitets grupp     |    500   |    Ja     |
|  Antal ögonblicks bilder per volym       |    255     |    Nej        |
|  Antal undernät som har delegerats till Azure NetApp Files (Microsoft. NetApp/Volumes) per Azure-Virtual Network    |   1   |    Nej    |
|  Maximalt antal IP-adresser som används i ett VNet (inklusive peer-virtuella nätverk) med Azure NetApp Files    |    1000   |    Nej   |
|  Minsta storlek på en pool med enskild kapacitet   |  4 TiB     |    Nej  |
|  Maximal storlek för en pool med enskild kapacitet    |  500 TiB   |   Nej   |
|  Minsta storlek på en enskild volym    |    100 GiB    |    Nej    |
|  Högsta tilldelade kvot för en enskild volym *   |   92 TiB   |    Nej   |
|  Maximal storlek på en enskild volym *     |    100 TiB    |    Nej       |

\* En volym kan skapas manuellt eller ändras till maximally 92 TiB. En volym kan dock växa upp till 100 TiB i ett överutnyttjande scenario. Mer information om kapacitet finns i [kostnads modell för Azure NetApp Files](azure-netapp-files-cost-model.md) . 

## <a name="request-limit-increase"></a>Begär gräns ökning 

Du kan skapa en support förfrågan för Azure för att öka de justerbara gränserna från tabellen ovan. 

Från Azure Portal navigerings plan: 

1. Klicka på **Hjälp + Support**.
2. Klicka på **+ ny supportbegäran**.
3. Ange följande information på fliken grundläggande: 
    1. Typ av problem: Välj **tjänst-och prenumerations gränser (kvoter)** .
    2. Prenumerationer: Välj prenumerationen för den resurs du behöver för att öka kvoten.
    3. Kvot typ: Välj **lagring: Azure NetApp Files gränser**.
    4. Klicka på **Nästa: Lösningar**.
4. På fliken information:
    1. I rutan Beskrivning anger du följande information för motsvarande resurs typ:

        |  Resource  |    Överordnade resurser      |    Begärda nya gränser     |    Orsak till kvot ökning       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärt nytt maximalt **konto** nummer*    |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Pool    |  *Prenumerations-ID, konto-URI*  |  *Begärt nytt nummer för högsta **pool***   |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Volym  |  *Prenumerations-ID, konto-URI, pool-URI*   |  *Begärt nytt maximalt **volym** nummer*     |  *Vilket scenario eller vilka användnings fall ber begäran?*  |

    2. Ange lämplig support metod och ange din kontrakts information.

    3. Klicka på **Nästa: Granska + skapa** för att skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnads modell för Azure NetApp Files](azure-netapp-files-cost-model.md)
