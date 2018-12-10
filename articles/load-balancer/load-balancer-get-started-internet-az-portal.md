---
title: Skapa en Standardbelastningsutjämnare med zonredundant klientdel med hjälp av Azure portal
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en offentlig Standard Load Balancer med zonredundant offentlig IP-adress klientdel med Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: fc5dcd76ca8ecfb820a8d157fee9cc0a92bd2d5b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135703"
---
#  <a name="create-a-public-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Skapa en offentlig Standard Load Balancer med zonredundant klientdel med hjälp av Azure portal

Den här artikeln visar hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) med en zonredundant klientdel som använder en offentlig IP-Standard-adress. En enda frontend-IP-adress på en Standard Load Balancer är zonredundant som standard.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
 Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Skapa en zon redundant load balancer

1. Från en webbläsare navigerar du till Azure portal: [ http://portal.azure.com ](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Längst upp till vänster på skärmen Välj **skapa en resurs** > **nätverk** > **belastningsutjämnare.**
3. I den **skapa belastningsutjämnare** sidan under **namn** typ **myLoadBalancer**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard**.
6. Klicka på **offentliga IP-adressen**, klickar du på **Skapa nytt**, och i **skapa offentlig IP-adress** sidan under namn, typ **myPublicIPStandard**.
    >[!NOTE] 
    > Offentligt IP som skapats i det här steget är för Standard-SKU och zonredundant som standard. 
8. Under **plats**väljer **östra usa2**, och klicka sedan på **OK**. Nu startar distributionen av lastbalanseraren. Distributionen tar några minuter att slutföra.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



