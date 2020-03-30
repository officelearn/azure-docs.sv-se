---
title: Resursgränser för Azure NetApp-filer | Microsoft-dokument
description: Beskriver begränsningar för Azure NetApp-filer resurser och hur man begär resursgräns ökning.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408835"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Genom att förstå resursgränser för Azure NetApp Files kan du hantera dina volymer.

## <a name="resource-limits"></a>Resursbegränsningar

I följande tabell beskrivs resursgränser för Azure NetApp-filer:

|  Resurs  |  Standardgräns  |  Justerbar via supportbegäran  |
|----------------|---------------------|--------------------------------------|
|  Antal NetApp-konton per Azure-region   |  10    |  Ja   |
|  Antal kapacitetspooler per NetApp-konto   |    25     |   Ja   |
|  Antal volymer per kapacitetspool     |    500   |    Ja     |
|  Antal ögonblicksbilder per volym       |    255     |    Inga        |
|  Antal undernät som delegerats till Azure NetApp-filer (Microsoft.NetApp/volymer) per Virtuellt Azure-nätverk    |   1   |    Inga    |
|  Antal använda IPs i ett virtuella nätverk (inklusive direkt peered VNets) med Azure NetApp-filer   |    1000   |    Ja   |
|  Minsta storlek på en pool med en kapacitet   |  4 TiB     |    Inga  |
|  Maximal storlek för en pool med en kapacitet    |  500 TiB (09)   |   Inga   |
|  Minsta storlek på en enskild volym    |    100 Gib (100 GiB)    |    Inga    |
|  Maximal storlek på en enskild volym     |    100 TiB (100)    |    Inga    |
|  Maximal storlek för en enskild fil     |    16 TiB    |    Inga    |    
|  Maximal storlek för en enskild katalog      |    320 MB    |    Inga    |    
|  Maximalt antal filer ([maxfiler](#maxfiles)) per volym     |    100 miljoner    |    Ja    |    

Mer information finns i [Vanliga frågor om kapacitetshantering](azure-netapp-files-faqs.md#capacity-management-faqs).

## <a name="maxfiles-limits"></a>Maxfiler gränser<a name="maxfiles"></a> 

Azure NetApp-filer har en gräns som kallas *maxfiles*. Maxfiles-gränsen är antalet filer som en volym kan innehålla. Maxfiles-gränsen för en Azure NetApp Files-volym indexeras baserat på volymens storlek (kvot). Maxfiles-gränsen för en volym ökar eller minskar med en hastighet av 20 miljoner filer per TiB av etablerad volymstorlek. 

Tjänsten justerar maxfiles-gränsen dynamiskt för en volym baserat på dess etablerade storlek. En volym som konfigurerades från början med en storlek på 1 TiB skulle till exempel ha en maxfiles-gräns på 20 miljoner. Efterföljande ändringar av volymens storlek skulle resultera i en automatisk justering av maxfiles-gränsen baserat på följande regler: 

|    Volymstorlek (kvot)     |  Automatisk justering av maxfiles-gränsen    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 miljoner     |
|    >= 1 TiB men < 2 TiB    |    40 miljoner     |
|    >= 2 TiB men < 3 TiB    |    60 miljoner     |
|    >= 3 TiB men < 4 TiB    |    80 miljoner     |
|    >= 4 TiB                |    100 miljoner    |

För alla volymstorlekar kan du initiera en [supportbegäran för](#limit_increase) att öka maxfiles-gränsen över 100 miljoner.

## <a name="request-limit-increase"></a>Ökning av gränsen för begäran<a name="limit_increase"></a> 

Du kan skapa en Azure-supportbegäran för att öka de justerbara gränserna från tabellen ovan. 

Från azure portal-navigeringsplan: 

1. Klicka på **Stöd + hjälp +**.
2. Klicka på **+ Ny supportbegäran**.
3. Ange följande information på fliken Grunderna: 
    1. Ärendetyp: Välj **Tjänst- och prenumerationsgränser (kvoter)**.
    2. Prenumerationer: Välj prenumeration för den resurs som du behöver kvoten ökat.
    3. Kvottyp: Välj **Lagring: Azure NetApp-filer begränsar**.
    4. Klicka på **Nästa: Lösningar**.
4. På fliken Information:
    1. Ange följande information för motsvarande resurstyp i rutan Beskrivning:

        |  Resurs  |    Överordnade resurser      |    Begärda nya gränser     |    Orsak till kvotökning       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärt nytt maximalt **kontonummer***    |  *Vilket scenario eller användningsfall föranledde begäran?*  |
        |  Pool    |  *Prenumerations-ID, Konto URI*  |  *Begärt nytt maximalt **poolnummer***   |  *Vilket scenario eller användningsfall föranledde begäran?*  |
        |  Volym  |  *Prenumerations-ID, Konto URI, Pool URI*   |  *Begärt nytt maximalt **volymnummer***     |  *Vilket scenario eller användningsfall föranledde begäran?*  |
        |  Maxfiler  |  *Prenumerations-ID, Konto URI, Pool URI, Volym URI*   |  *Begärt nytt **maxfilsnummer***     |  *Vilket scenario eller användningsfall föranledde begäran?*  |    

    2. Ange lämplig supportmetod och ange din kontraktsinformation.

    3. Klicka på **Nästa: Granska + skapa** för att skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md)
