---
title: "Konfigurera privata IP-adresser för virtuella datorer (klassisk) - Azure-portalen | Microsoft Docs"
description: "Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer (klassisk) med Azure-portalen."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bde6de3495c2909b63b1f85e420a4ff5e7ac2c1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurera privat IP-adresser för en virtuell dator (klassisk) som använder Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i Resource Manager-distributionsmodellen](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Nedanstående exempel räknar en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet först skapa testmiljön som beskrivs i [skapa ett vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernätet i ett VNet med namnet *TestVNet* med en statisk privat IP-adress för *192.168.1.101*, Följ stegen nedan:

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **ny** > **Compute** > **Windows Server 2012 R2 Datacenter**, Lägg märke till att den **Välj en distributionsmodell** redan lista visas **klassiska**, och klicka sedan på **skapa**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. I den **skapa VM** bladet anger du namnet på den virtuella datorn skapas (*DNS01* i vårt exempel), lokalt administratörskonto och lösenord.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Klicka på **valfri konfiguration** > **nätverk** > **virtuellt nätverk**, och klicka sedan på **TestVNet**. Om **TestVNet** är inte tillgänglig, kontrollera att du använder den *centrala USA* plats och har skapat testmiljön som beskrivs i början av den här artikeln.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. I den **nätverk** bladet Kontrollera undernätet markerade *klientdel*, klicka på **IP-adresser**under **IP-adresstilldelning** klickar du på **statiska**, och ange sedan *192.168.1.101* för **IP-adress** som visas nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Klicka på **OK** i den **IP-adresser** bladet klicka sedan på **OK** i den **nätverk** bladet och klicka på **OK** i den **valfria config** bladet.
7. I den **skapa VM** bladet, klickar du på **skapa**. Lägg märke till panelen nedan visas i instrumentpanelen.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Utför stegen nedan om du vill visa statisk privat IP-adressinformation för den virtuella datorn skapas med stegen ovan.

1. Azure-Azure-portalen klickar du på **Bläddra bland alla** > **virtuella datorer (klassisk)** > **DNS01** > **alla inställningar** > **IP-adresser** och Lägg märke till tilldelning av IP-adresser och IP-adress som du ser nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Följ stegen nedan om du vill ta bort statisk privat IP-adress från den virtuella datorn skapade ovan.

1. Från den **IP-adresser** bladet som visas ovan, klickar du på **dynamiska** till höger om **IP-adresstilldelning**, klicka på **spara**, och klicka sedan på **Ja**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Följ stegen nedan om du vill lägga till en statisk privat IP-adress till den virtuella datorn skapas med stegen ovan:

1. Från den **IP-adresser** bladet som visas ovan, klickar du på **statiska** till höger om **IP-adresstilldelning**.
2. Typen *192.168.1.101* för **IP-adress**, klicka på **spara**, och klicka sedan på **Ja**.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).

