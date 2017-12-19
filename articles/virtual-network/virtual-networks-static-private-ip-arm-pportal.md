---
title: "Konfigurera privata IP-adresser för virtuella datorer - Azure-portalen | Microsoft Docs"
description: "Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer med hjälp av Azure portal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 672462fad715758e50680fa5bade4b1f9d50e6e5
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurera privat IP-adresser för en virtuell dator med hjälp av Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure-portalen (klassisk)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klassisk)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (klassisk)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också [hantera statisk privat IP-adress i den klassiska distributionsmodellen](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Nedanstående exempel räknar en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet först skapa testmiljön som beskrivs i [skapa ett vnet](virtual-networks-create-vnet-arm-pportal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Så här skapar du en virtuell dator för att testa statisk privat IP-adresser
Du kan inte ange en statisk privat IP-adress under genereringen av en virtuell dator i Resource Manager distribution läge med hjälp av Azure portal. Du måste först skapa den virtuella datorn är textmarkering dess privata IP-adress vara statisk.

Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernätet i ett VNet med namnet *TestVNet*, Följ stegen nedan.

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **ny** > **Compute** > **Windows Server 2012 R2 Datacenter**, Lägg märke till att den **Välj en distributionsmodell** redan lista visas **Resource Manager**, och klicka sedan på **skapa**, som visas i bilden nedan.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. I den **grunderna** bladet anger du namnet på den virtuella datorn skapas (*DNS01* i vårt exempel), lokalt administratörskonto och lösenord, som visas i bilden nedan.
   
    ![Bladet Grundläggande inställningar](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Se till att den **plats** valts är *centrala USA*, klicka sedan på **Välj befintlig** under **resursgruppen**, klicka på **resursgruppen** igen och klicka sedan på *TestRG*, och klicka sedan på **OK**.
   
    ![Bladet Grundläggande inställningar](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. I den **välja en storlek** bladet väljer **A1 Standard**, och klicka sedan på **Välj**.
   
    ![Välj ett blad storlek](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. I den **inställningar** bladet kontrollera följande egenskaper har ställts in med värdena nedan, och klicka sedan på **OK**.
   
    -**Lagringskontot**: *vnetstorage*
   
   * **Nätverket**: *TestVNet*
   * **Undernät**: *klientdel*
     
     ![Välj ett blad storlek](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. I den **sammanfattning** bladet, klickar du på **OK**. Lägg märke till panelen nedan visas i instrumentpanelen.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Utför stegen nedan om du vill visa statisk privat IP-adressinformation för den virtuella datorn skapas med stegen ovan.

1. Azure-Azure-portalen klickar du på **Bläddra bland alla** > **virtuella datorer** > **DNS01** > **alla inställningar** > **nätverksgränssnitt** och klicka sedan på endast nätverksgränssnittet som anges.
   
    ![Distribuera Virtuella sida vid sida](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. I den **nätverksgränssnittet** bladet klickar du på **alla inställningar** > **IP-adresser** och Lägg märke till de **tilldelning** och **IP-adress** värden.
   
    ![Distribuera Virtuella sida vid sida](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Följ stegen nedan om du vill lägga till en statisk privat IP-adress till den virtuella datorn skapas med stegen ovan:

1. Från den **IP-adresser** bladet som visas ovan, klickar du på **statiska** under **tilldelning**.
2. Typen *192.168.1.101* för **IP-adress**, och klicka sedan på **spara**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Om när du klickar på **spara** du märker att tilldelningen fortfarande är inställd på **dynamisk**, innebär det att IP-adressen som du har angett används redan. Prova en annan IP-adress.
> 
> 

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Utför följande steg för att ta bort statisk privat IP-adress från den virtuella datorn skapade ovan:

Från den **IP-adresser** bladet som visas ovan, klickar du på **dynamiska** under **tilldelning**, och klicka sedan på **spara**.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [reserverade offentliga IP-Adressen](virtual-networks-reserved-public-ip.md) adresser.
* Lär dig mer om [instansnivå offentliga IP-går](virtual-networks-instance-level-public-ip.md) adresser.
* Läs den [reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx).

