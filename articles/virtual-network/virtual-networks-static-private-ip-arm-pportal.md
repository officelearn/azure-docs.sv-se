---
title: Konfigurera privata IP-adresser för virtuella datorer – Azure-portalen | Microsoft Docs
description: Lär dig hur du konfigurerar den privata IP-adresser för virtuella datorer med Azure-portalen.
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
ms.openlocfilehash: e5efe0516d1b2dd387532d31a0a6654e6651fe41
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011734"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurera privata IP-adresser för en virtuell dator med Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure-portalen (klassisk)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (klassisk)](virtual-networks-static-private-ip-classic-ps.md)
> * [Azure CLI (klassisk)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också [hantera statiska privata IP-adressen i den klassiska distributionsmodellen](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Följande exempel förväntar sig en enkel miljö som redan har skapats. Om du vill att köra stegen visas i det här dokumentet, först skapa testmiljön som beskrivs i [skapa ett virtuellt nätverk](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Så här skapar du en virtuell dator för att testa statiska privata IP-adresser
Du kan inte ange en statisk privat IP-adress under genereringen av en virtuell dator i distributionsläget för Resource Manager-med hjälp av Azure portal. Du måste skapa den virtuella datorn först och sedan ange dess privata IP-adress till vara statisk.

Skapa en virtuell dator med namnet *DNS01* i den *klientdel* undernät i ett virtuellt nätverk med namnet *TestVNet*, Följ dessa steg:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **skapa en resurs** > **Compute** > **Windows Server 2012 R2 Datacenter**, Observera att den **väljer du en distribution modellen** redan lista visas **Resource Manager**, och klicka sedan på **skapa**enligt följande bild.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. I den **grunderna** fönstret anger du namnet på den virtuella datorn för att skapa (*DNS01* i scenariot), lokalt administratörskonto och lösenord, som visas i följande bild.
   
    ![Fönstret grunder](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Kontrollera att den **plats** valts är *centrala USA*, klicka sedan på **Välj befintlig** under **resursgrupp**, klicka sedan på **Resursgrupp** igen och klicka sedan på *TestRG*, och klicka sedan på **OK**.
   
    ![Fönstret grunder](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. I den **väljer du en storlek** väljer **Standard A1**, och klicka sedan på **Välj**.
   
    ![Välj en storlek-fönstret](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. I den **inställningar** rutan kontrollerar du att egenskaperna är inställda med följande värden och klickar sedan på **OK**.
   
    -**Storage-konto**: *vnetstorage*
   
   * **Nätverk**: *TestVNet*
   * **Undernät**: *Klientdel*
     
     ![Välj en storlek-fönstret](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. I den **sammanfattning** fönstret klickar du på **OK**. Observera följande panel visas på instrumentpanelen.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs, t.ex när [tilldela flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statiska privata IP-adressinformation för en virtuell dator
Om du vill visa statiska privata IP-adressinformation för den virtuella datorn skapas med stegen ovan, kör du följande steg.

1. Azure-portalen klickar du på **Bläddra bland alla** > **virtuella datorer** > **DNS01** > **alla inställningar**  >  **Nätverksgränssnitt** och klicka sedan på den enda nätverksgränssnitt som visas.
   
    ![Panelen distribuerar VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. I den **nätverksgränssnittet** fönstret klickar du på **alla inställningar** > **IP-adresser** och Lägg märke till de **tilldelning** och  **IP-adress** värden.
   
    ![Panelen distribuerar VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Hur du lägger till en statisk privat IP-adress till en befintlig virtuell dator
Följ dessa steg för att lägga till en statisk privat IP-adress till den virtuella datorn som skapats med hjälp av stegen ovan:

1. Från den **IP-adresser** fönstret som visas ovan, klickar du på **statiska** under **tilldelning**.
2. Typ *192.168.1.101* för **IP-adress**, och klicka sedan på **spara**.
   
    ![Skapa virtuell dator i Azure-portalen](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Om när du klickar på **spara**, du märker att tilldelningen fortfarande är inställd på **dynamisk**, betyder det IP-adress som du har angett används redan. Prova en annan IP-adress.
> 
> 

Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs, t.ex när [tilldela flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Följ anvisningarna nedan om du vill ta bort den statiska privata IP-adressen från den virtuella datorn som skapades ovan:

Från den **IP-adresser** fönstret som visas ovan, klickar du på **dynamisk** under **tilldelning**, och klicka sedan på **spara**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Vi rekommenderar att du inte statiskt tilldelar privat IP-adress som tilldelats virtuella Azure-datorer i operativsystemet på en virtuell dator, om inte behövs, t.ex när [tilldela flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt anger den privata IP-adressen i operativsystemet, kontrollera att det är samma adress som den privata IP-adress som tilldelats Azure [nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller du kan förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressen](virtual-network-network-interface-addresses.md#private) inställningar. Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg

Läs om hur du hanterar [IP-adressinställningarna](virtual-network-network-interface-addresses.md).

