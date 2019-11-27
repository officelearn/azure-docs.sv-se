---
title: Skapa en Load Balancer med en zonindelade-frontend-Azure Portal
titleSuffix: Azure Load Balancer
description: Lär dig hur du skapar en Standard Load Balancer med zonindelade-frontend med Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225345"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Skapa en Standard Load Balancer med zonindelade-frontend med Azure Portal

Den här artikeln beskriver hur du skapar en offentlig [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) med en IP-konfiguration för zonindelade-frontend. Information om hur tillgänglighets zoner fungerar med Standard Load Balancer finns i [standard Load Balancer och tillgänglighets zoner](load-balancer-standard-availability-zones.md). 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Stöd för Tillgänglighetszoner är tillgänglig för väljer Azure-resurser och regioner och VM-storlekar. Mer information om hur du kommer igång och vilka Azure-resurser, regioner och VM-storlekar som du kan prova tillgänglighets zoner med finns i [Översikt över Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Du kan få support via [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) eller genom att [öppna ett Azure-supportärende](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Skapa en belastningsutjämnare med zonindelad klientdelens IP-adress

1. Från en webbläsare navigerar du till Azure Portal: [https://portal.azure.com](https://portal.azure.com) och logga in med ditt Azure-konto.
2. På den övre vänstra sidan av skärmen väljer du **skapa en resurs** > **nätverks** > **Load Balancer.**
3. På sidan **skapa belastningsutjämnare** under **namn** skriver du **myLoadBalancer**.
4. Under **Typ** väljer du **Offentlig**.
5. Under SKU väljer du **standard**.
6. Klicka på **Välj en offentlig IP-adress**, klicka på **Skapa ny**och sidan **skapa offentlig IP-adress** under namn, Skriv **myPublicIPZonal**, för SKU, Välj **standard**, för tillgänglighets zon, väljer du **1**.
    
>[!NOTE] 
> Offentligt IP som skapats i det här steget är av Standard-SKU som standard.

1. För **resurs grupp**klickar du på **Skapa ny**och skriver sedan **myResourceGroupZLB** som namn på resurs gruppen.
1. För **plats**väljer du **Västeuropa**, Västeuropa och klickar sedan på **OK**. Nu startar distributionen av lastbalanseraren. Distributionen tar några minuter att slutföra.

    ![Skapa zon – redundanta Standard Load Balancer med Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Nästa steg
- Läs mer om [standard Load Balancer-och tillgänglighets zoner](load-balancer-standard-availability-zones.md).



