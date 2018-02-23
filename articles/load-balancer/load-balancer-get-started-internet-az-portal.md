---
title: "Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure portal | Microsoft Docs"
description: "Lär dig hur du skapar en offentlig belastningen belastningsutjämnaren Standard med zonredundant offentlig IP-adress klientdel med Azure-portalen"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: b9b4275497798db5310f2a28aeb6c582b1a7429c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Skapa en offentlig belastningen belastningsutjämnaren Standard med zonredundant klientdel för offentlig IP-adress med hjälp av Azure portal

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel med en Standard offentliga IP-adress.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrera sig för tillgänglighet zoner, Load Balancer Standard och offentliga IP-Standard förhandsversion
 
>[!NOTE]
[Läsa in belastningsutjämning Standard SKU](https://aka.ms/azureloadbalancerstandard) är för närvarande under förhandsgranskning. Som förhandsversion kanske funktionen inte har samma tillgänglighet och pålitlighet som funktioner som är allmänt tillgängliga. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Använd den allmänt tillgängliga [Load Balancer grundläggande SKU](load-balancer-overview.md) för produktion-tjänster. 

> [!NOTE]
> Tillgänglighet zoner i förhandsversionen och är redo för din utveckla och testa scenarier. Support är tillgänglig för väljer Azure-resurser och regioner och familjer för VM-storlek. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och Virtuella storlek familjer kan du försöka tillgänglighet zoner med finns [översikt av tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Innan du väljer en zon eller zonredundant alternativ för klientdel offentliga IP-adressen för belastningsutjämnaren, måste du slutföra stegen i [registrera sig för tillgänglighet zoner förhandsversion](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Skapa en zon redundant belastningsutjämnare

1. Öppna en webbläsare och gå till Azure Portal: [http://portal.azure.com](http://portal.azure.com). Logga in med ditt Azure-konto.
2. På den översta vänstra sidan av skärmen väljer **skapar du en resurs** > **nätverk** > **belastningsutjämnaren.**
3. I den ** skapa belastningsutjämnare, under **namn** typen **myPublicLB**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard (förhandsgranskning)**.
6. Klicka på **offentliga IP-adressen**, klickar du på **Skapa nytt**på den **skapa en offentlig IP-adress** sidan under namn, typ **myPublicIPStandard**, och för **tillgänglighet zonen (förhandsgranskning)**väljer **redundantzonen**.
7. Under **plats**väljer **östra US2**, och klicka sedan på **OK**. Nu startar distributionen av belastningsutjämnaren. Distributionen tar några minuter att slutföra.

    ![Skapa zonredundant Load Balancer Standard med Azure-portalen](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Nästa steg
- Lär dig hur [skapa en offentlig IP-adress i en zon för tillgänglighet](../virtual-network/create-public-ip-availability-zone-portal.md)



