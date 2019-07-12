---
title: Skapa ett virtuellt nätverk – Snabbstart – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Ett virtuellt nätverk kan Azure-resurser, som virtuella datorer, kommunicera säkert med varandra och med internet
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: bbc40ae358a6ac7f58e01de997728db21c7eb3bc
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839720"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med Azure Portal

Ett virtuellt nätverk är grundläggande byggstenar för ditt privata nätverk i Azure. Det gör att Azure-resurser, t.ex. virtuella datorer (VM) för säker kommunikation med varandra och med internet. I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med Azure-portalen. Sedan kan kan du distribuera två virtuella datorer i det virtuella nätverket, på ett säkert sätt kommunicera mellan de två virtuella datorerna och ansluter till de virtuella datorerna från internet.


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
    | Subscription | Välj din prenumeration.|
    | Resource group | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Location | Välj **USA, östra**.|
    | Undernät – namn | Ange *myVirtualSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |

1. Lämna resten som standard och välj **skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Längst upp till vänster på skärmen Välj **skapa en resurs** > **Compute** > **Windows Server 2019 Datacenter**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Value |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Subscription | Välj din prenumeration. |
    | Resource group | Välj **myResourceGroup**. Du skapade du i föregående avsnitt. |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVm1*. |
    | Region | Välj **USA, östra**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Image | Låt standardvärdet **Windows Server 2019 Datacenter**. |
    | Size | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användarnamn som du väljer. |
    | lösenordsinställning | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator – diskar** lämnar du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – nätverk** väljer du följande information:

    | Inställning | Value |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna kvar standardinställningen **myVirtualNetwork**. |
    | Subnet | Lämna standardinställningen **myVirtualSubnet (10.1.0.0/24)** . |
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.

1. Välj **Nästa: Hantering**.

1. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

1. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myvmstorageaccount*. Om det här namnet tas, skapar du ett unikt namn.|
    | Typ av konto | Lämna kvar standardinställningen **Lagring (generell användning v1)** . |
    | Prestanda | Lämna kvar standardinställningen **Standard**. |
    | Replikering | Lämna kvar standardinställningen **Lokalt redundant lagring (LRS)** . |

1. Välj **OK**

1. Välj **Granska + skapa**. Kommer du till den **granska + skapa** sidan där Azure verifierar din konfiguration.

1. När du ser den **valideringen har slutförts** meddelandet markerar **skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

1. Slutför steg 1 och 9 ovan.

    > [!NOTE]
    > För **Virtuellt datornamn** i steg 2 anger du *myVm2*.
    >
    > I steg 7, **Diagnostiklagringskonto**, ska du se till att välja **myvmstorageaccount**.

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure verifierar din konfiguration.

1. När du ser den **valideringen har slutförts** meddelandet markerar **skapa**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

När du har skapat *myVm1*, ansluta till internet.

1. I portalens sökfältet anger du *myVm1*.

1. Välj knappen **Anslut**.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

    När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade *.rdp*-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange användarnamnet och lösenordet du angav när du skapar den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

1. Ange `ping myVm2`.

    Du får ett meddelande som liknar detta:

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

    Det här kommandot kan ICMP inkommande via Windows-brandväggen:

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

    Du får svar från *myVm1*, eftersom du har tillåtit ICMP via Windows-brandväggen på den *myVm1* VM i steg 3.

1. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna, ta bort resursgruppen och alla resurser den innehåller:

1. Ange *myResourceGroup* i den **Search** rutan högst upp i portalen och välj **myResourceGroup** från sökresultaten.

1. Välj **Ta bort resursgrupp**.

1. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har skapat du ett virtuellt standardnätverk och två virtuella datorer. Du är ansluten till en virtuell dator från internet och kommunicerat på ett säkert sätt mellan de två virtuella datorerna. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md).

Som standard tillåter Azure obegränsad säker kommunikation mellan virtuella datorer. I motsats till detta tillåter den endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om hur du konfigurerar olika typer av nätverkskommunikation för virtuella datorer finns i självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
