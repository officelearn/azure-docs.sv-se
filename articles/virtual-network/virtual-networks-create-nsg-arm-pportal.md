---
title: Skapa en nätverkssäkerhetsgrupp - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar och distribuerar nätverkssäkerhetsgrupper med Azure-portalen.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Skapa en säkerhetsgrupp för nätverk med Azure-portalen

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också [skapa NSG: er i den klassiska distributionsmodellen](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Skapa NSG-klientdel NSG: N
Att skapa den **NSG-klientdel** NSG enligt scenariot gör du följande:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Välj **+ skapa en resurs >** > **Nätverkssäkerhetsgrupper**.
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Under **Nätverkssäkerhetsgrupper**väljer **Lägg till**.
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Under **skapa nätverkssäkerhetsgruppen**, skapa en NSG som heter *NSG-klientdel* i den *RG NSG* resurs gruppen och välj sedan **skapa** .
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Skapa regler i en befintlig nätverkssäkerhetsgrupp (NSG)
Utför följande steg för att skapa regler i en befintlig NSG från Azure portal:

1. Välj **alla tjänster**, Sök sedan efter **Nätverkssäkerhetsgrupper**. När **Nätverkssäkerhetsgrupper** visas, markerar du den.
2. Välj i listan över NSG: er, **NSG-klientdel** > **inkommande säkerhetsregler**
   
    ![Azure portal – NSG-klientdel](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. I listan över **inkommande säkerhetsregler**väljer **Lägg till**.
   
    ![Azure portal – Lägg till regel](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Under **Lägg till ingående säkerhetsregel**, skapa en regel med namnet *regel för web* med prioriteten för *200* åtkomst via *TCP* till port *80* till någon virtuell dator från en datakälla och väljer sedan **OK**. Observera att de flesta av de här inställningarna är standardvärden redan.
   
    ![Azure portal – regelinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Efter några sekunder visas den nya regeln i NSG: N.
   
    ![Azure portal – ny regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Upprepa steg 6 för att skapa en regel med namnet *rdp-regel* med prioritet *250* åtkomst via *TCP* till port *3389* till någon virtuell dator från en källa.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associera nätverkssäkerhetgruppen (NSG) med FrontEnd-undernätet

1. Välj **alla tjänster >**, ange **resursgrupper**väljer **resursgrupper** när den visas sedan välja **RG NSG**.
2. Under **RG NSG**väljer **...**   >  **TestVNet**.
   
    ![Azure portal – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Under **inställningar**väljer **undernät** > **klientdel** > **nätverkssäkerhetsgruppen**  >  **NSG-klientdel**.
   
    ![Azure portal – undernätsinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. I den **klientdel** bladet väljer **spara**.
   
    ![Azure portal – undernätsinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Skapa NSG BackEnd NSG: N
Att skapa den **NSG BackEnd** NSG och koppla den till den **BackEnd** undernät, gör du följande:

1. Så här skapar du en NSG som heter *NSG BackEnd*, upprepa stegen i [skapa NSG-klientdel NSG](#Create-the-NSG-FrontEnd-NSG).
2. Att skapa den **inkommande** regler i tabellen nedan, upprepa stegen i [skapa regler i en befintlig NSG](#Create-rules-in-an-existing-NSG).
   
   | Regel för inkommande trafik | Regel för utgående trafik |
   | --- | --- |
   | ![Azure portal - regel för inkommande trafik](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure portal - regel för utgående trafik](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Associera den **NSG Backend** NSG till den **BackEnd** undernät, upprepa stegen i [koppla NSG till undernätet FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera befintliga NSG: er](manage-network-security-group.md)
* [Aktivera loggning](virtual-network-nsg-manage-log.md) för NSG: er.