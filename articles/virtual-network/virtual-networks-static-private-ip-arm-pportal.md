---
title: Konfigurera privata IP-adresser för virtuella datorer – Azure Portal
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: b1019b15463a03282c5d1bd8f0a878433d7f488e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199571"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurera privata IP-adresser för en virtuell dator med hjälp av Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Följande exempel steg förväntar sig en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet skapar du först test miljön som beskrivs i [skapa ett virtuellt nätverk](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Så här skapar du en virtuell dator för att testa statiska privata IP-adresser
Du kan inte ange en statisk privat IP-adress när du skapar en virtuell dator i distributions läget i Resource Manager med hjälp av Azure Portal. Du måste först skapa den virtuella datorn och sedan ange dess privata IP-adress som statisk.

Följ dessa steg om du vill skapa en virtuell dator med namnet *DNS01* i *klient delens* undernät med namnet *TestVNet*:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **skapa en resurs** > **Compute** > **Windows Server 2012 R2 Data Center**, Lägg märke till att listan **Välj en distributions modell** redan innehåller **Resource Manager**och klicka sedan på **skapa**, som visas i följande figur.
   
    ![Skapa virtuell dator i Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. I fönstret **grundläggande** anger du namnet på den virtuella dator som ska skapas (*DNS01* i scenariot), det lokala administratörs kontot och lösen ordet som visas i följande bild.
   
    ![Fönstret grundläggande](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Kontrol lera att den valda **platsen** är *Central USA*, klicka sedan på **Välj befintlig** under **resurs grupp**, klicka på **resurs grupp** igen och klicka sedan på *TestRG*. Klicka sedan på **OK**.
   
    ![Fönstret grundläggande](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. I fönstret **Välj en storlek** väljer du **a1 standard**och klickar sedan på **Välj**.
   
    ![Välj ett storleks fönster](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. I fönstret **Inställningar** kontrollerar du att egenskaperna har angetts med följande värden och klickar sedan på **OK**.
   
    -**lagrings konto**: *vnetstorage*
   
   * **Nätverk**: *TestVNet*
   * **Undernät**: *FrontEnd*
     
     ![Välj ett storleks fönster](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. I fönstret **Sammanfattning** klickar du på **OK**. Observera följande panel som visas på instrument panelen.
   
    ![Skapa virtuell dator i Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs, t. ex. När [du tilldelar flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du se till att den är samma adress som den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](virtual-network-network-interface-addresses.md#private) . Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hur du hämtar statiska privata IP-adressinformation för en virtuell dator
Utför följande steg för att visa information om statisk privat IP-adress för den virtuella datorn som skapades med stegen ovan.

1. Klicka på **Bläddra alla** > **virtuella datorer** > **DNS01** > **alla inställningar** > **nätverks gränssnitt** från Azure Portal och klicka sedan på det enda nätverks gränssnitt som visas.
   
    ![Panelen för att distribuera VM](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. I fönstret **nätverks gränssnitt** klickar du på **alla inställningar** > **IP-adresser** och noterar **tilldelnings** **-och IP-adress** värden.
   
    ![Panelen för att distribuera VM](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Så här lägger du till en statisk privat IP-adress till en befintlig virtuell dator
Följ dessa steg om du vill lägga till en statisk privat IP-adress till den virtuella datorn som skapades med stegen ovan:

1. I rutan **IP-adresser** som visas ovan klickar du på **statisk** under **tilldelning**.
2. Skriv *192.168.1.101* för **IP-adress**och klicka sedan på **Spara**.
   
    ![Skapa virtuell dator i Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Om du efter att ha klickat på **Spara**, Observera att tilldelningen fortfarande är inställd på **dynamisk**, betyder det att den angivna IP-adressen redan används. Försök med en annan IP-adress.
> 
> 

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs, t. ex. När [du tilldelar flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du se till att den är samma adress som den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](virtual-network-network-interface-addresses.md#private) . Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Slutför följande steg för att ta bort den statiska privata IP-adressen från den virtuella datorn som skapades ovan:

I rutan **IP-adresser** som visas ovan klickar du på **dynamisk** under **tilldelning**och sedan på **Spara**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs, t. ex. När [du tilldelar flera IP-adresser till en virtuell Windows-dator](virtual-network-multiple-ip-addresses-portal.md). Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du se till att den är samma adress som den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings), eller så kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](virtual-network-network-interface-addresses.md#private) . Du bör aldrig manuellt tilldela offentliga IP-adress som tilldelats till en Azure virtuell dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att hantera [Inställningar för IP-adresser](virtual-network-network-interface-addresses.md).

