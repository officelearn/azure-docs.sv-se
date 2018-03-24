---
title: Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure portal | Microsoft Docs
description: Lär dig hur du skapar en offentlig belastningen belastningsutjämnaren Standard med zonredundant offentlig IP-adress klientdel med Azure-portalen
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
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure portal

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel med en Standard offentliga IP-adress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="register-for-availability-zones-preview"></a>Registrera dig för förhandsgranskning tillgänglighet zoner
 
Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Skapa en zon redundant belastningsutjämnare

1. Från en webbläsare navigerar du till Azure portal: [ http://portal.azure.com ](http://portal.azure.com) och logga in med ditt Azure-konto.
2. På den översta vänstra sidan av skärmen väljer **skapar du en resurs** > **nätverk** > **belastningsutjämnaren.**
3. I den ** skapa belastningsutjämnare, under **namn** typen **myPublicLB**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard (förhandsgranskning)**.
6. Klicka på **offentliga IP-adressen**, klickar du på **Skapa nytt**på den **skapa en offentlig IP-adress** sidan under namn, typ **myPublicIPStandard**, och för **tillgänglighet zonen (förhandsgranskning)**väljer **redundantzonen**.
7. Under **plats**väljer **östra US2**, och klicka sedan på **OK**. Nu startar distributionen av belastningsutjämnaren. Distributionen tar några minuter att slutföra.

    ![Skapa zonredundant Load Balancer Standard med Azure-portalen](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Nästa steg
- Lär dig hur [skapa en offentlig IP-adress i en zon för tillgänglighet](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



