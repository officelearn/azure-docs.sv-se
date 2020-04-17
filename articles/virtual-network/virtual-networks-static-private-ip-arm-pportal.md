---
title: Konfigurera privata IP-adresser för virtuella datorer - Azure-portal
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer med Hjälp av Azure-portalen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461556"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurera en privat IP-adress för en virtuell dator med Azure-portalen

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Följande exempelsteg förväntar sig att en enkel miljö redan ska skapas. Om du vill köra stegen som de visas i det här dokumentet skapar du först [ett virtuellt nätverk](quick-create-portal.md#create-a-virtual-network). Men i steg 3 använder du dessa värden i stället:

| Inställning | Värde |
| ------- | ----- |
| Namn | *TestVNet* |
| Adressutrymme | *192.168.0.0/16* |
| Resursgrupp | **TestRG** (om det behövs väljer du **Skapa ny** för att skapa den) |
| Undernät – Namn | *Frontend* |
| Undernät – adressintervall | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Skapa en virtuell dator för testning av statiska privata IP-adresser
När du skapar en virtuell dator i distributionsläge för Resource Manager kan du inte ange en statisk privat IP-adress med Azure-portalen. I stället skapar du den virtuella datorn först. Då kan du ställa in dess privata IP vara statisk.

Så här skapar du en virtuell dator med namnet *DNS01* i *FrontEnd-undernätet* i ett virtuellt nätverk med namnet *TestVNet:*

1. På [Portal-menyn i Azure](https://portal.azure.com) väljer du **Skapa en resurs**.

    ![Skapa en resurs, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Välj **Beräkna** > **virtuell dator**.

    ![Skapa VM, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. I **Basics**anger du värden för objekt som beskrivs i följande tabell. Välj sedan **Nästa:&nbsp;&nbsp;Diskar** och sedan **Nästa:&nbsp;&nbsp;Nätverk**.

    | Objekt | Värde |
    | --- | --- |
    | **Prenumeration** | Din nuvarande prenumeration |
    | **Resursgrupp** | **TestRG** (välj från listrutan) |
    | **Namn på virtuell dator** | *DNS01 (PÅ)* |
    | **Regionen** | **Jag är mycket bra på att vi måste ta tid på Östra USA** |
    | **Bild** | **Windows Server 2019 Datacenter** |
    | **Storlek** | **VM-storlek** **på B1ls**, **Erbjudande** av **standard** |
    | **Användarnamn** | Användarnamnet för ditt administratörskonto |
    | **Lösenord** | Lösenordet för administratörskontots användarnamn |
    | **Bekräfta lösenord** | Lösenordet igen |

    ![Fliken Grunderna, Skapa en virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. Ange värden för objekt som beskrivs i följande tabell i **Nätverk**och välj sedan **Nästa**.

    | Objekt | Värde |
    | --- | --- |
    | **Virtuellt nätverk** | **TestVNet** |
    | **Undernät** | **Frontend** |

    ![Fliken Nätverk, Skapa en virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. I **Management**väljer du **vnetstorage**under **Diagnostiklagringskonto**. Om lagringskontot inte visas i listan väljer du **Skapa nytt**, anger ett **namn på** *vnetstorage*och väljer **OK**. Slutligen väljer du **Granska&nbsp;+&nbsp;skapa**.

    ![Fliken Hantering, Skapa en virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Granska översiktsinformationen i **Granska + skapa**och välj sedan **Skapa**.

    ![Fliken Granska + skapa, skapa en virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Följande meddelande visas när den virtuella datorn har skapats.

![Meddelande om slutförande av distribution, Skapa en virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Hämta privat IP-adressinformation för en virtuell dator
Så här visar du den privata IP-adressinformationen för den nya virtuella datorn:

1. Gå till [Azure-portalen](https://portal.azure.com) för att hitta din virtuella dator. Sök efter och välj **Virtuella datorer**.

    ![Virtuella datorer, sökruta, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Välj namnet på den nya virtuella datorn (**DNS01**).

    ![Lista över virtuella datorer, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Välj **Nätverk**och välj det enda nätverksgränssnittet i listan.

    ![Nätverksgränssnitt, nätverk, virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Välj **IP-konfigurationer**och välj IP-konfigurationen i tabellen.

    ![IP-konfiguration, Nätverksgränssnitt, nätverk, virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. I **privata IP-adressinställningar,** under det virtuella **testvnet/frontend-nätverket/undernätet,** notera **tilldelningsvärdet** (**dynamiskt** eller **statiskt)** och **IP-adressen**.

    ![Dynamisk eller statisk tilldelning, gamla privata IP-adressinställningar, IP-konfiguration, Nätverksgränssnitt, nätverk, virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Lägga till en statisk privat IP-adress till en befintlig virtuell dator
Så här lägger du till en statisk privat IP-adress till den nya virtuella datorn:

1. På sidan IP-konfiguration anger du tilldelningen för din privata IP-adress till **Static**.
2. Ändra din privata **IP-adress** till *192.168.1.101*och välj sedan **Spara**.
   
    ![Dynamisk eller statisk tilldelning, nya privata IP-adressinställningar, IP-konfiguration, Nätverksgränssnitt, nätverk, virtuell dator, Azure-portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Om du märker när du har valt **Spara** att tilldelningen fortfarande är inställd på **Dynamisk**används redan IP-adressen som du skrev. Prova en annan IP-adress.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Så här tar du bort den statiska privata IP-adressen från den virtuella datorn:

På sidan IP-konfiguration anger du tilldelningen för den privata IP-adressen till **Dynamisk**och väljer sedan **Spara**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser i operativsystemet

Inifrån operativsystemet för en virtuell dator bör du inte statiskt tilldela den *privata* IP som har tilldelats Azure VM. Gör endast statisk tilldelning av en privat IP när det är nödvändigt, till exempel när [du tilldelar många IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md). Om du ställer in den privata IP-adressen manuellt i operativsystemet kontrollerar du att den matchar den privata IP-adressen som tilldelats [Azure-nätverksgränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings). Annars kan du förlora anslutningen till den virtuella datorn. Läs mer om [privata IP-adressinställningar.](virtual-network-network-interface-addresses.md#private)

Du bör heller aldrig manuellt tilldela den *offentliga* IP-adressen som tilldelats en virtuell Azure-dator i den virtuella datorns operativsystem.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar [IP-adressinställningar](virtual-network-network-interface-addresses.md).
