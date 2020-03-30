---
title: Skapa ett virtuellt nätverk – Snabbstart – Azure-portalen
titleSuffix: Azure Virtual Network
description: 'Snabbstart: Skapa ett virtuellt nätverk i Azure-portalen. Dessa nätverk låter Azure-resurser, som virtuella datorer, kommunicera säkert med varandra och internet.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240074"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure-portalen

I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med Azure-portalen. Du distribuerar två virtuella datorer (VMs). Därefter kommunicerar du säkert mellan virtuella datorer och ansluter till virtuella datorer från internet. Ett virtuellt nätverk är den grundläggande byggstenen för ditt privata nätverk i Azure. Det gör det möjligt för Azure-resurser, som virtuella datorer, att kommunicera säkert med varandra och med Internet.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. På Portal-menyn i Azure väljer du **Skapa en resurs**. Välj > **Nätverksvirtiska nätverk**på Azure Marketplace . **Networking**

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Namn | Ange *myVirtualNetwork*. |
    | Location | Välj **östra USA**.|

1. Välj **Nästa: IP-adresser**och för **IPv4-adressutrymme**anger du *10.1.0.0/16*.

1. Välj **Lägg till undernät**och ange sedan *myVirtualSubnet* för **undernätsnamn** och *10.1.0.0/24* för **undernätsadressintervall**.

1. Välj **Lägg till**och välj sedan Granska + **skapa**. Lämna resten som standard och välj **Skapa**.

1. Välj **Skapa**i **Skapa virtuellt nätverk**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. På Portal-menyn i Azure väljer du **Skapa en resurs**.

1. Välj **Beräkna** > **Windows Server 2019 Datacenter**på Azure Marketplace . Välj **Skapa**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **Projektinformation** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du har skapat den här resursgruppen i föregående avsnitt. |
    | **Information om instans** |  |
    | Namn på virtuell dator | Ange *myVm1*. |
    | Region | Välj **östra USA**. |
    | Alternativ för tillgänglighet | Standard för **Ingen infrastrukturredundans krävs**. |
    | Bild | Standard för **Windows Server 2019 Datacenter**. |
    | Storlek | Standard **DS1 v2**som standard . |
    | **Administratörskonto** |  |
    | Användarnamn | Ange ett användarnamn som du väljer. |
    | lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **Regler för inkommande port** |  |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Ange *HTTP (80)* och *RDP (3389)*. |
    | **Spara pengar** |  |
    | Har du redan en Windows-licens? | Standard för **nr**. |

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator - Diskar**behåller du standardinställningarna och väljer **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Standard till **myVirtualNetwork**. |
    | Undernät | Standard till **myVirtualSubnet (10.1.0.0/24)**. |
    | Offentlig IP-adress | Standard till **(nya) myVm-ip**. |
    | Säkerhetsgrupp för nätverksnätverk för nätverkskort | Standard för **Basic**. |
    | Offentliga inkommande portar | Tillåt **att markerade portar är standard**. |
    | Välj inkommande portar | Standard till **HTTP** och **RDP**.

1. Välj **Nästa: Management**.

1. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

1. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myvmstorageaccount*. Om det här namnet tas skapar du ett unikt namn.|
    | Typ av konto | Standard lagring **(allmänt ändamål v1)**. |
    | Prestanda | Standard som **standard**. |
    | Replikering | Standard för **lokalt redundant lagring (LRS)**. |

1. Välj **OK**och välj sedan **Granska + skapa**. Du tas till sidan **Granska + skapa** där Azure validerar din konfiguration.

1. När meddelandet **Validering har skickats** väljer du **Skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Upprepa proceduren i föregående avsnitt för att skapa en annan virtuell dator.

> [!IMPORTANT]
> För **namnet på den virtuella datorn**anger du *myVm2*.
>
> För **lagringskonto för diagnoser**kontrollerar du att du väljer **myvmstorageaccount**i stället för att skapa ett.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från Internet

När du har skapat *myVm1*ansluter du till internet.

1. Sök efter och välj *myVm1*i Azure-portalen .

1. Välj **Anslut**och sedan **RDP**.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

    Sidan **Anslut** öppnas.

1. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna RDP-filen. Välj **Anslut** om du uppmanas att göra det.

1. Ange det användarnamn och lösenord som du angav när du skapade den virtuella datorn.

    > [!NOTE]
    > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto**för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn.

1. Välj **OK**.

1. Du kan få en certifikatvarning när du loggar in. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

1. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

1. Ange `ping myVm2`.

    Du får ett meddelande som liknar den här utdata:

    ```output
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
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Med det här kommandot kan ICMP inkommande via Windows-brandväggen:

1. Stäng fjärrskrivbordsanslutningen till *myVm1*.

1. Utför stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet) igen, men anslut till *myVm2*.

1. Från en kommandotolk anger du `ping myvm1`.

    Du får tillbaka något som liknar det här meddelandet:

    ```output
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

    Du får svar från *myVm1*, eftersom du tillät ICMP via Windows-brandväggen på *myVm1* VM i steg 3.

1. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

I den här snabbstarten har du skapat ett virtuellt standardnätverk och två virtuella datorer. Du har anslutit till en virtuell dator från internet och kommunicerade säkert mellan de två virtuella datorerna.

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resursgruppen och alla resurser som den innehåller:

1. Sök efter och välj *myResourceGroup*.

1. Välj **Ta bort resursgrupp**.

1. Ange *myResourceGroup* för **TYP RESURSGRUPPNAMN** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information om inställningar för virtuella nätverk finns i [Skapa, ändra eller ta bort ett virtuellt nätverk](manage-virtual-network.md).

Som standard tillåter Azure säker kommunikation mellan virtuella datorer. Azure tillåter endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om typer av vm-nätverkskommunikation finns i [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
