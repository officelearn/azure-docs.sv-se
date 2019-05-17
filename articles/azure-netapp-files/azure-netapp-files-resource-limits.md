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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826380"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Resursbegränsningar för Azure NetApp Files

Så här fungerar resursgränser för Azure NetApp Files kan du hantera dina volymer.

## <a name="resource-limits"></a>Resursbegränsningar

I följande tabell beskrivs resursgränser för Azure NetApp-filer:

|  Resource  |  Standardgräns  |  Inställbara genom en supportförfrågan  |
|----------------|---------------------|--------------------------------------|
|  Antal NetApp-konton per Azure-prenumeration   |  10    |  Ja   |
|  Antal kapacitet pooler per NetApp-konto   |    25     |   Ja   |
|  Antalet volymer per pool kapacitet     |    500   |    Ja     |
|  Antalet ögonblicksbilder per volym       |    255     |    Nej        |
|  Antalet undernät som har delegerats till Azure NetApp-filer (Microsoft.NetApp/volumes) per Azure-nätverk    |   1   |    Nej    |
|  Maximalt antal virtuella datorer (inklusive peerkopplade virtuella nätverk) som kan ansluta till en volym     |    1000   |    Nej   |
|  Minsta storlek på en enda kapacitet-pool   |  4 TiB     |    Nej  |
|  Maximal storlek på en enda kapacitet-pool    |  500 TiB   |   Nej   |
|  Minsta storlek på en enskild volym    |    100 giB    |    Nej    |
|  Högsta tilldelade kvot på en enda volym *   |   92 TiB   |    Nej   |
|  Maximal storlek på en enda volym *     |    100 TiB    |    Nej       |

* En volym kan skapas manuellt eller ändra storlek till högst 92 TiB. Men kan en volym växa upp till 100 TiB i ett scenario för överförbrukning. Se [kostnadsmodell för Azure NetApp Files](azure-netapp-files-cost-model.md) mer information om kapacitet överförbrukning. 

## <a name="request-limit-increase"></a>Gräns för begäran 

Du kan skapa en Azure-supportbegäran öka justerbara gränserna från tabellen ovan. 

Från Azure portalnavigering plan: 

1. Klicka på **hjälp + support**.
2. Klicka på **+ ny supportbegäran**.
3. Ange följande information på fliken grunderna: 
    1. Typ av problem: Välj **begränsningar för tjänsten och -prenumeration (kvoter)**.
    2. Prenumerationer: Välj prenumerationen för den resurs som du behöver kvoten.
    3. Typ av kvot: Välj **lagring: Azure NetApp filer begränsar**.
    4. Klicka på **Nästa: Lösningar**.
4. På fliken information:
    1. Ange följande information för den motsvarande resurstypen i rutan Beskrivning:

        |  Resource  |    Överordnad resurser      |    Begärda nya gränserna     |    Orsaken till kvot       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Konto |  *Prenumerations-ID*   |  *Begärda nya maximalt **konto** tal*    |  *Vilka scenario eller Använd om du uppmanas att göra begäran?*  |
        |  Pool    |  *Prenumerations-ID, konto URI*  |  *Begärda nya maximalt **pool** tal*   |  *Vilka scenario eller Använd om du uppmanas att göra begäran?*  |
        |  Volym  |  *Prenumerations-ID, konto URI, Pool URI*   |  *Begärda nya maximalt **volym** tal*     |  *Vilka scenario eller Använd om du uppmanas att göra begäran?*  |

    2. Ange rätt stöd för metoden och ange ditt kontraktsinformation.

    3. Klicka på **Nästa: Granska + skapa** skapa begäran. 


## <a name="next-steps"></a>Nästa steg  

- [Förstå lagringshierarkin för Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Kostnad modell för Azure NetApp-filer](azure-netapp-files-cost-model.md)
