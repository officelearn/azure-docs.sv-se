---
title: "Skapa ett virtuellt nätverk med Azure-portalen | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: ba7a67b8ae57da165f45bd3552a3dfac5f4ef64b
ms.openlocfilehash: c7257faad9a41174fb1d65e04c99cd96a8af3ea9


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Skapa ett virtuellt nätverk med Azure-portalen

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure har två distributionsmodeller: Azure Resource Manager och klassisk. Microsoft rekommenderar att skapa resurser med Resource Manager-distributionsmodellen. Mer information om skillnaderna mellan de två modellerna finns i artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Förstå Azure-distributionsmodellerna).
 
Den här artikeln förklarar hur du skapar ett virtuellt nätverk (VNet) med hjälp av Resource Manager-distributionsmodellen via Azure Portal. Du kan också skapa ett virtuellt nätverk med Resource Manager med hjälp av andra verktyg eller skapa ett virtuellt nätverk med hjälp av den klassiska distributionsmodellen genom att välja ett annat alternativ i följande lista:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Mall](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (klassisk)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (klassisk)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (klassisk)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Om du vill skapa ett virtuellt nätverk med hjälp av Azure Portal följer du dessa steg:

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **Nytt** > **Nätverk** > **Virtuellt nätverk**, som du ser i följande bild:

    ![Nytt virtuellt nätverk](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. På bladet **Virtuellt nätverk** som visas kontrollerar du att *Resource Manager* är markerat och klickar sedan på **Skapa**, som du ser i följande bild:

    ![Virtual Network](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. På bladet **Skapa virtuellt nätverk** som visas anger du *TestVNet* för **Namn**, *192.168.0.0/16* för **Adressutrymme**, *FrontEnd* för **Undernätsnamn**, *192.168.1.0/24* för **Undernätsadressintervall**, *TestRG* för **Resursgrupp**, väljer din **Prenumeration**, en **Plats** och klickar på knappen **Skapa**, som du ser i följande bild:

    ![Skapa det virtuella nätverket](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Du kan också välja en befintlig resursgrupp. Mer information om resursgrupper finns i [översikten över Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). Du kan också välja en annan plats. Mer information om Azure-platser och Azure-regioner finns i artikeln [Azure-regioner](https://azure.microsoft.com/regions).

5. På portalen kan du bara skapa ett undernät när du skapar ett virtuellt nätverk. I det här scenariot måste ett andra undernät skapas när det virtuella nätverket har skapats. Du skapar ett andra undernät genom att klicka på **Alla resurser** och sedan på **TestVNet** på bladet **Alla resurser**, som du ser i följande bild:

    ![Ett virtuellt nätverk som skapats](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. På bladet **TestVNet** som visas klickar du på **Undernät** och sedan på **+Undernät**, anger *BackEnd* för **Namn**, *192.168.2.0/24* för **Adressintervall** på bladet **Lägg till undernät** och klickar sedan på **OK**, som du ser i följande bild:

    ![Lägga till undernät](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. De två undernäten visas, som du ser i följande bild:
    
    ![Lista med undernät i VNet](./media/virtual-network-create-vnet-arm-pportal/6.png)

I den här artikeln har du lärt dig hur du skapar ett virtuellt nätverk med två undernät för testning. Innan du skapar ett virtuellt nätverk för produktion rekommenderar vi att du läser [översikten över virtuella nätverk](virtual-networks-overview.md) och artikeln om hur du [planerar och utformar virtuella nätverk](virtual-network-vnet-plan-design-arm.md) så att du verkligen förstår hur virtuella nätverk och alla inställningar fungerar. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du ansluter:

- En virtuell dator (VM) till ett virtuellt nätverk genom att läsa artiklarna om hur du [skapar en Windows-baserad virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller [en Linux-baserad virtuell dator](../virtual-machines/virtual-machines-linux-quick-create-portal.md). I stället för att skapa ett virtuellt nätverk och undernät i stegen i artikeln kan du ansluta en virtuell dator till ett befintligt virtuellt nätverk och undernät.
- Det virtuella nätverket till andra virtuella nätverk genom att läsa artikeln [Connect VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) (Ansluta virtuella nätverk).
- Det virtuella nätverket till ett lokalt nätverk med hjälp av ett virtuellt privat nätverk (VPN) för plats till plats eller en ExpressRoute-krets. Mer information finns i artiklarna [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Ansluta ett virtuellt nätverk till ett lokalt nätverk med hjälp av VPN för plats till plats) och [Link a VNet to an ExpressRoute circuit](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) (Länka ett virtuellt nätverk till en ExpressRoute-krets).


<!--HONumber=Jan17_HO1-->


