---
title: Skapa en belastningsutjämnare med en zonindelad frontend - Azure-portalen
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en Standardbelastningsutjämnare med zonindelad klientdel med Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0a746ace4806495b17462b506f030811247240e2
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247199"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Skapa en Standardbelastningsutjämnare med zonindelad klientdel med hjälp av Azure portal

Den här artikeln visar hur du skapar en offentlig [Standardbelastningsutjämnare](https://aka.ms/azureloadbalancerstandard) med en zonindelad klientdelens IP-konfiguration. Information om hur tillgänglighetszoner fungerar med Standard Load Balancer finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner, och VM-storlekar som du kan prova tillgänglighetszoner med finns i [översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Skapa en belastningsutjämnare med zonindelad klientdelens IP-adress

1. Från en webbläsare navigerar du till Azure portal: [ http://portal.azure.com ](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Längst upp till vänster på skärmen Välj **skapa en resurs** > **nätverk** > **belastningsutjämnare.**
3. I den **skapa belastningsutjämnare** sidan under **namn** typ **myLoadBalancer**.
4. Under **Typ** väljer du **Offentlig**.
5. Välj under SKU, **Standard**.
6. Klicka på **välja en offentlig IP-adress**, klickar du på **Skapa nytt**, och i **skapa offentlig IP-adress** sidan under namn, typ **myPublicIPZonal**, SKU, Välj **Standard**, tillgänglighetszon, Välj **1**.
    
>[!NOTE] 
> Offentligt IP som skapats i det här steget är av Standard-SKU som standard.

7. För **resursgrupp**, klickar du på **Skapa nytt**, och skriv sedan **myResourceGroupZLB** som namnet på resursgruppen.
8. För **plats**väljer **Västeuropa**, och klicka sedan på **OK**. Nu startar distributionen av lastbalanseraren. Distributionen tar några minuter att slutföra.

    ![Skapa zonredundant Standardbelastningsutjämnare med Azure portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).



