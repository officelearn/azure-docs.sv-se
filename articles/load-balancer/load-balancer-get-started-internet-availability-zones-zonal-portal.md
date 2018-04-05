---
title: Skapa en offentlig belastningen belastningsutjämnaren Standard med zonal klientdel för offentlig IP-adress med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du skapar en offentlig belastningen belastningsutjämnaren Standard med zonal offentlig IP-adress klientdel med Azure-portalen
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 533c48b3a004f85dfbd2970d73dcf7de21811dca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonal klientdel för offentlig IP-adress med hjälp av Azure portal

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonal klientdel. För att förstå hur tillgänglighet zoner fungerar med Standard belastningsutjämnare, se [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Stöd för tillgänglighet zoner är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Skapa en belastningsutjämnare med zonal klientdelens IP-adress

1. Från en webbläsare navigerar du till Azure portal: [ http://portal.azure.com ](http://portal.azure.com) och logga in med ditt Azure-konto.
2. På den översta vänstra sidan av skärmen väljer **skapar du en resurs** > **nätverk** > **belastningsutjämnaren.**
3. I den **skapa belastningsutjämnaren** sidan under **namn** typen **myLoadBalancer**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard**.
6. Klicka på **Välj en offentlig IP-adress**, klickar du på **Skapa nytt**, och i **skapa offentlig IP-adress** sidan under namn, typ **myPublicIPZonal**, SKU, Välj **Standard**, tillgänglighet zon, Välj **1**.
    
>[!NOTE] 
> Offentligt IP som skapats i det här steget är för Standard SKU: N som standard.

7. För **resursgruppen**, klickar du på **Skapa nytt**, och skriv sedan **myResourceGroupZLB** som namnet på resursgruppen.
8. För **plats**väljer **Västeuropa**, och klicka sedan på **OK**. Nu startar distributionen av belastningsutjämnaren. Distributionen tar några minuter att slutföra.

    ![Skapa zonredundant Load Balancer Standard med Azure-portalen](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



