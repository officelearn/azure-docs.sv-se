---
title: Konfigurera privata IP-adresser för virtuella datorer - Azure-portalen | Microsoft Docs
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer med hjälp av Azure portal.
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
ms.openlocfilehash: ff9d025980f80cb77246ea12dbf2e9bcedd73f86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423285"
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

Följande exempel räknar med en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet först skapa testmiljön som beskrivs i [skapa ett virtuellt nätverk](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Så här skapar du en virtuell dator för att testa statisk privat IP-adresser
Du kan inte ange en statisk privat IP-adress under genereringen av en virtuell dator i Resource Manager distribution läge med hjälp av Azure portal. Du måste skapa den virtuella datorn först och sedan ange sitt privata IP-adress vara statisk.

Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernätet i ett VNet med namnet *TestVNet*, gör du följande:

1. Navigera till http://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **skapar du en resurs** > **Compute** > **Windows Server 2012 R2 Datacenter**, Lägg märke till att den **väljer du en distribution modellen** redan lista visas **Resource Manager**, och klicka sedan på **skapa**, som visas i följande bild.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. I den **grunderna** rutan anger du namnet på den virtuella datorn för att skapa (*DNS01* i scenariot), lokalt administratörskonto och lösenord, som visas i följande bild.
   
    ![Grunderna i fönstret](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Se till att den **plats** valts är *centrala USA*, klicka sedan på **Välj befintlig** under **resursgruppen**, klicka på **resursgruppen** igen och klicka sedan på *TestRG*, och klicka sedan på **OK**.
   
    ![Grunderna i fönstret](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. I den **välja en storlek** väljer **A1 Standard**, och klicka sedan på **Välj**.
   
    ![Välj en storlek fönstret](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. I den **inställningar** och kontrollera att egenskaperna konfigureras med följande värden och klicka sedan på **OK**.
   
    -**Lagringskontot**: *vnetstorage*
   
   * **Nätverket**: *TestVNet*
   * **Undernät**: *klientdel*
     
     ![Välj en storlek fönstret](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. I den **sammanfattning** rutan klickar du på **OK**. Observera följande panelen visas på instrumentpanelen.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt, t.ex när [tilldela flera IP-adresser till en Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats i Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Lär dig mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statisk privat IP-adressinformation för en virtuell dator
Utför följande steg om du vill visa statisk privat IP-adressinformation för den virtuella datorn skapas med stegen ovan.

1. Azure-portalen klickar du på **Bläddra bland alla** > **virtuella datorer** > **DNS01** > **alla inställningar**  >  **Nätverksgränssnitt** och klicka sedan på endast nätverksgränssnittet som anges.
   
    ![Distribuera Virtuella sida vid sida](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. I den **nätverksgränssnittet** rutan klickar du på **alla inställningar** > **IP-adresser** och Lägg märke till de **tilldelning** och  **IP-adress** värden.
   
    ![Distribuera Virtuella sida vid sida](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Följ dessa steg för att lägga till en statisk privat IP-adress till den virtuella datorn skapas med stegen ovan:

1. Från den **IP-adresser** ovan, klickar du på **statiska** under **tilldelning**.
2. Typen *192.168.1.101* för **IP-adress**, och klicka sedan på **spara**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Om när du klickar på **spara**, du märker att tilldelningen fortfarande är inställd på **dynamisk**, innebär det IP-adress som du har angett används redan. Prova en annan IP-adress.
> 
> 

Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt, t.ex när [tilldela flera IP-adresser till en Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats i Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Lär dig mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Utför följande steg för att ta bort statisk privat IP-adress från den virtuella datorn skapade ovan:

Från den **IP-adresser** ovan, klickar du på **dynamiska** under **tilldelning**, och klicka sedan på **spara**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privata IP-Adressen som tilldelats den virtuella Azure-datorn i operativsystemet på en virtuell dator, om nödvändigt, t.ex när [tilldela flera IP-adresser till en Windows-VM](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats i Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Lär dig mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du hanterar [IP-adressinställningarna](virtual-network-network-interface-addresses.md).

