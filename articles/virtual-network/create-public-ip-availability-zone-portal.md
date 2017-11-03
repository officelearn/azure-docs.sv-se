---
title: Skapa en zoned offentliga IP-adress med Azure Portal | Microsoft Docs
description: "Skapa en offentlig IP-adress i en zon för tillgänglighet med Azure-portalen."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 2fcbed2f83d66a0b4336cd1c464bb02eff3ef229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-portal"></a>Skapa en offentlig IP-adress i en zon för tillgänglighet med Azure-portalen

Du kan distribuera en offentlig IP-adress i en zon för Azure tillgänglighet (förhandsversion). En zon för tillgänglighet är ett fysiskt separat zon i en Azure-region. Lär dig att:

> * Skapa en offentlig IP-adress i en zon för tillgänglighet
> * Identifiera relaterade resurser som skapats i zonen tillgänglighet

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på https://portal.azure.com. 

## <a name="create-a-zonal-public-ip-address"></a>Skapa en zonal offentliga IP-adress

1. Klicka på knappen **New** (Nytt) i det övre vänstra hörnet i Azure Portal.
2. Välj **nätverk**, och välj sedan **offentliga IP-adressen**.
3. Ange eller Välj värden för följande inställningar, väljer din prenumeration, Godkänn standardinställningarna för de återstående inställningarna och sedan klicka på **skapa**:

    |Inställning|Värde|
    |---|---|
    |SKU| **Grundläggande**: tilldelade med allokeringsmetoden statisk eller dynamisk. Kan tilldelas till Azure-resurser som kan tilldelas en offentlig IP-adress, till exempel nätverksgränssnitt, VPN-gatewayer, Programgatewayer och Internetriktade belastningsutjämnare. Du kan tilldela den offentliga IP-adressen till en viss zon i den **tillgänglighet zonen** inställningen. Är inte zonredundant. Mer information om tillgänglighetszoner finns i [Översikt över tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). **Standard**: tilldelade med statisk tilldelningsmetod-metoden. Kan tilldelas nätverksgränssnitt eller standard Internetriktade belastningsutjämnare. Om du tilldelar den offentliga IP-adressen till en standard Internetriktade belastningsutjämnare, måste du välja Standard. Mer information om SKU:er för belastningsutjämnare i Azure finns i [Standard-SKU för Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Är zonredundant, som standard. Kan skapas zonindelat och garanteras i en viss tillgänglighetszon. Standard-SKU är i förhandsversion. Innan du skapar en offentlig IP-adress för Standard-SKU, måste du först registrera för förhandsgranskningen. Information om hur du registrerar dig för förhandsversionen finns i [registrera dig för förhandsversionen av standard-SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#preview-sign-up). Standard-SKU kan bara skapas i en plats som stöds.  En lista över platser som stöds (regioner) finns i [Regional tillgänglighet](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#region-availability) och du kan övervaka sidan [Uppdateringar för Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network) för ytterligare regionsstöd.|   
    |Namn|Namnet måste vara unikt inom resursgruppen som du väljer.|
    |Resursgrupp|Klicka på Skapa nytt och ange myResourceGroup|
    |Plats|Västra Europa|
    |Tillgänglighet zon|Om du har valt den **Standard** SKU, du kan välja *Zonredundant* om du vill att IP-adress för att hantera över zoner. Om du väljer den **grundläggande** SKU, IP-adressen inte är flexibel över zoner. Oavsett SKU som du väljer, kan du tilldela adressen till en viss zon, om du väljer. |

    Inställningarna visas i portalen, enligt följande bild:

    ![Skapa zonal offentlig IP-adress](./media/create-public-ip-availability-zone-portal/public-ip-address.png) 

> [!NOTE]
> När du tilldelar en offentlig IP-adress för standard-SKU till en virtuell dators nätverksgränssnitt måste du uttryckligen tillåta den avsedda trafiken med en [nätverkssäkerhetsgrupp](security-overview.md#network-security-groups). Kommunikationen med resursen misslyckas tills du har skapat och kopplat en nätverkssäkerhetsgrupp och uttryckligen tillåtit önskad trafik.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Lär dig mer om [offentliga IP-adresser](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)