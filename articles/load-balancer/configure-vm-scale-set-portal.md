---
title: Konfigurera skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer - Azure-portal
description: Lär dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: b2a83b226a4b2ddbbd554783de6a2b5c85c92f0a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349714"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Konfigurera en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer med Azure-portalen

I den här artikeln får du lära dig hur du konfigurerar en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer. 

## <a name="prerequisites"></a>Krav

- En Azure-prenumeration.
- En befintlig standard sku belastningsutjämnare i prenumerationen där den virtuella datorn skala set kommer att distribueras.
- Ett Virtuellt Azure-nätverk för skalningsuppsättningen för den virtuella datorn.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera skalningsuppsättning för virtuella datorer med befintlig belastningsutjämnare

I det här avsnittet skapar du en skala för virtuella datorer i Azure-portalen med en befintlig Azure-belastningsutjämnare.

> [!NOTE]
> Följande steg förutsätter att ett virtuellt nätverk med namnet **myVNet** och en Azure-belastningsutjämnare med namnet **myLoadBalancer** tidigare har distribuerats.

1. Klicka på Skapa **en skala** > **Compute** > uppsättning skala för virtuell dator för virtuell**dator** eller sök efter **skala för virtuella datorer** på marketplace-sökningen längst upp till vänster på skärmen.

2. Välj **Skapa**.

3. Ange eller välj den här informationen på fliken **Grunderna** i **Skapa en skala**för virtuella datorer:

    | Inställning                        | Värde                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Projektinformation**            |                                                                                                       |
    | Prenumeration                   | Välj din Azure-prenumeration                                                                        |
    | Resursgrupp                 | Välj Skapa ny, ange **myResourceGroup**och välj sedan OK eller välj en befintlig resursgrupp. |
    | **Information om skalningsuppsättning**          |                                                                                                       |
    | Namn på VM-skalningsuppsättning | Ange **myVMSS**                                                                                      |
    | Region                         | Välj **östra USA 2**                                                                                    |
    | Tillgänglighetszon              | Välj **Ingen**                                                                                       |
    | **Information om instans**           |                                                                                                       |
    | Bild                          | Välj **Ubuntu Server 18.04 LTS**                                                                    |
    | Azure Spot-instans            | Välj **Nej**                                                                                         |
    | Storlek                           | Lämna som standard                                                                                      |
    | **Administratörskonto**      |                                                                                                       |
    | Autentiseringstyp            | Välj **lösenord**                                                                                   |
    | Användarnamn                       | Ange ditt administratörsanvändarnamn        |
    | lösenord                       | Ange ditt administratörslösenord    |
    | Bekräfta lösenord               | Ange administratörslösenordet igen |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Skapa skaluppsättning för virtuella datorer." border="true":::

4. Välj fliken **Nätverk.**

5. Ange eller välj den här informationen på fliken **Nätverk:**

     Inställning                           | Värde                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfiguration av virtuellt nätverk** |                                                          |
    | Virtuellt nätverk                   | Välj **myVNet** eller ditt befintliga virtuella nätverk.      |
    | **Belastningsutjämning**                |                                                          |
    | Använda en belastningsutjämnare               | Välj **Ja**                                           |
    | **Inställningar för belastningsutjämning**       |                                                          |
    | Alternativ för belastningsutjämning            | Välj **Azure-belastningsutjämnare**                           |
    | Välj en belastningsutjämnare            | Välj **myLoadBalancer** eller din befintliga belastningsutjämnare |
    | Välj en backend-pool             | Välj **myBackendPool** eller din befintliga backend-pool.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Skapa skaluppsättning för virtuella datorer." border="true":::

6. Välj fliken **Hantering.**

7. Ange **Boot diagnostics** på **Av**på fliken **Hantering** .

8. Välj den blå **knappen Granska + skapa.**

9. Granska inställningarna och välj knappen **Skapa.**

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalningsuppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalningsuppsättningar och belastningsutjämnare för virtuella datorer finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är VM-skalningsuppsättningar?](../virtual-machine-scale-sets/overview.md)
