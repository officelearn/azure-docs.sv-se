---
title: Skapa ett virtuellt nätverk – Snabbstart – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 11/30/2018
ms.author: kumud
ms.openlocfilehash: 995bc8e7b2eb4e9160b2b625067f20324df2cbfd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717010"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med Azure Portal

Med ett virtuellt nätverk kan Azure-resurser, till exempel virtuella datorer, kommunicera privat med varandra och med Internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk. När du har skapat ett virtuellt nätverk distribuerar du två virtuella datorer i det virtuella nätverket. Sedan ansluter du till de virtuella datorerna från Internet och kommunicerar privat mellan de två virtuella datorerna.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **USA, östra**.|
    | Undernät – namn | Ange *myVirtualSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |

1. Lämna resten av standardinställningarna och välj **Skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Välj **Skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter** längst upp till vänster på skärmen.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade den i det förra avsnittet. |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm1*. |
    | Region | Välj **USA, östra**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Image | Lämna kvar standardinställningen **Windows Server 2016 Datacenter**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett valfritt användarnamn. |
    | Lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna kvar standardinställningen **myVirtualNetwork**. |
    | Undernät | Lämna standardinställningen **myVirtualSubnet (10.1.0.0/24)**. |
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Nätverkssäkerhetsport | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.

1. Välj **Nästa: Hantering**.

1. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

1. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myvmstorageaccount*. |
    | Typ av konto | Lämna kvar standardinställningen **Lagring (generell användning v1)**. |
    | Prestanda | Lämna kvar standardinställningen **Standard**. |
    | Replikering | Lämna kvar standardinställningen **Lokalt redundant lagring (LRS)**. |

1. Välj **OK**

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure verifierar din konfiguration.

1. När du ser **Valideringen godkänd** väljer du **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

1. Slutför steg 1 och 9 ovan.

    > [!NOTE]
    > För **Virtuellt datornamn** i steg 2 anger du *myVm2*.
    >
    > I steg 7, **Diagnostiklagringskonto**, ska du se till att välja **myvmstorageaccount**.

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure verifierar din konfiguration.

1. När du ser **Valideringen godkänd** väljer du **Skapa**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

När du har skapat *myVm1* ansluter du till den via internet.

1. I portalens sökfältet anger du *myVm1*.

1. Välj knappen **Anslut**.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

    När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade *.rdp*-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

1. Ange `ping myVm2`.

    Du får tillbaka något som liknar det här meddelandet:

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` misslyckas eftersom `ping` använder Internet Control Message Protocol (ICMP). Som standard tillåts inte ICMP genom Windows-brandväggen.

1. Ange det efterföljande kommandot för att tillåta *myVm2* att pinga *myVm1* i ett senare skede:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Kommandot tillåter ICMP inkommande genom Windows-brandväggen:

1. Stäng fjärrskrivbordsanslutningen till *myVm1*.

1. Utför stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet) igen, men anslut till *myVm2*.

1. Från en kommandotolk anger du `ping myvm1`.

    Du får tillbaka något som liknar det här meddelandet:

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Du får svar från *myVm1*, eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn *myVm1* i ett tidigare steg.

1. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuellt nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser den innehåller:

1. Skriv *myResourceGroup* i rutan **Sök** högst upp i portalen.

1. När du ser **myResourceGroup** i sökresultatet väljer du den.

1. Välj **Ta bort resursgrupp**.

1. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från Internet och kommunicerat privat mellan de två virtuella datorerna. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md).

Som standard tillåter Azure obegränsad privat kommunikation mellan virtuella datorer. I motsats till detta tillåter den endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om hur du konfigurerar olika typer av nätverkskommunikation för virtuella datorer finns i självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).