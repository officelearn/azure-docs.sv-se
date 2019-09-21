---
title: Resurs gränser för Azure NetApp Files | Microsoft Docs
description: Beskriver gränser för Azure NetApp Files resurser och hur du begär ökning av resurs gränsen.
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
ms.date: 09/20/2019
ms.author: b-juche
ms.openlocfilehash: f7213ddee5d7bdfd41508f5fee66de63cde5b7c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170034"
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
|  Antal IP-adresser i ett VNet (inklusive peer-virtuella nätverk) som har åtkomst till Azure NetApp Files   |    1000   |    Ja   |
|  Minsta storlek på en pool med enskild kapacitet   |  4 TiB     |    Nej  |
|  Maximal storlek för en pool med enskild kapacitet    |  500 TiB   |   Nej   |
|  Minsta storlek på en enskild volym    |    100 GiB    |    Nej    |
|  Maximal storlek på en enskild volym     |    100 TiB    |    Nej    |
|  Maximalt antal filer ([maxfiles](#maxfiles)) per volym     |    100 000 000    |    Ja    |    
|  Maximal storlek för en enskild fil     |    16 TiB    |    Nej    |    

## Maxfiles-gränser<a name="maxfiles"></a> 

Azure NetApp Files volymer har en gräns som kallas *maxfiles*. Gränsen för maxfiles är antalet filer som en volym kan innehålla. Maxfiles-gränsen för en Azure NetApp Files volym indexeras baserat på volymens storlek (kvot). Maxfiles-gränsen för en volym ökar eller minskar med hastigheten på 20 000 000 filer per TiB med etablerad volym storlek. 

Tjänsten justerar dynamiskt maxfiles-gränsen för en volym baserat på dess etablerade storlek. Till exempel skulle en volym som har kon figurer ATS ursprungligen med en storlek på 1 TiB ha en maxfiles gräns på 20 000 000. Efterföljande ändringar av volymens storlek skulle leda till automatisk omanpassning av maxfiles-gränsen baserat på följande regler: 

|    Volym storlek (kvot)     |  Automatisk justering av maxfiles-gränsen    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 000 000     |
|    > = 1 TiB men < 2 TiB    |    40 000 000     |
|    > = 2 TiB men < 3 TiB    |    60 000 000     |
|    > = 3 TiB men < 4 TiB    |    80 000 000     |
|    > = 4 TiB                |    100 000 000    |

För alla volym storlekar kan du initiera en [supportbegäran](#limit_increase) för att öka maxfiles-gränsen bortom 100 000 000.

## Begär gräns ökning<a name="limit_increase"></a> 

Du kan skapa en support förfrågan för Azure för att öka de justerbara gränserna från tabellen ovan. 

Från Azure Portal navigerings plan: 

1. Klicka på **Hjälp + Support**.
2. Klicka på **+ ny supportbegäran**.
3. Ange följande information på fliken grundläggande: 
    1. Typ av problem: Välj **tjänst-och prenumerations gränser (kvoter)** .
    2. Prenumerationer Välj prenumerationen för den resurs du behöver för att öka kvoten.
    3. Typ av kvot: Välj **lagring: Azure NetApp Files gränser**.
    4. Klicka på **Nästa: Lösningar**.
4. På fliken information:
    1. I rutan Beskrivning anger du följande information för motsvarande resurs typ:

        |  Resource  |    Överordnade resurser      |    Begärda nya gränser     |    Orsak till kvot ökning       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärt nytt maximalt **konto** nummer*    |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Pool    |  *Prenumerations-ID, konto-URI*  |  *Begärt nytt nummer för högsta **pool***   |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Volym  |  *Prenumerations-ID, konto-URI, pool-URI*   |  *Begärt nytt maximalt **volym** nummer*     |  *Vilket scenario eller vilka användnings fall ber begäran?*  |
        |  Maxfiles  |  *Prenumerations-ID, konto-URI, pool-URI, volym-URI*   |  *Begärt nytt maximalt **maxfiles** -nummer*     |  *Vilket scenario eller vilka användnings fall ber begäran?*  |    

    2. Ange lämplig support metod och ange din kontrakts information.

    3. Klicka på **Nästa: Granska + skapa** för att skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnads modell för Azure NetApp Files](azure-netapp-files-cost-model.md)
