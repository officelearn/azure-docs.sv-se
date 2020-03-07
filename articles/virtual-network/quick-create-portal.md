---
title: Skapa ett virtuellt nätverk – Snabbstart – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här snabbstarten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Ett virtuellt nätverk gör det möjligt för Azure-resurser, t. ex. virtuella datorer, att kommunicera på ett säkert sätt med varandra och med Internet
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
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393120"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Snabbstart: Skapa ett virtuellt nätverk med hjälp av Azure-portalen

Ett virtuellt nätverk är det grundläggande Bygg blocket för ditt privata nätverk i Azure. Det gör det möjligt för Azure-resurser, till exempel virtuella datorer, att kommunicera på ett säkert sätt med varandra och med Internet. I den här snabb starten får du lära dig hur du skapar ett virtuellt nätverk med hjälp av Azure Portal. Sedan kan du distribuera två virtuella datorer i det virtuella nätverket, kommunicera säkert mellan de två virtuella datorerna och ansluta till de virtuella datorerna från Internet.


Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Från Azure Portal-menyn väljer du **skapa en resurs**.

2. Från Azure Marketplace väljer du **nätverk** > **virtuellt nätverk**.

3. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Name | Ange *myVirtualNetwork*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj sedan **OK**. |
    | Plats | Välj **USA, östra**.|
    | Undernät – Namn | Ange *myVirtualSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |

4. Lämna resten som standard och välj **skapa**.

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket:

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Från Azure Portal-menyn väljer du **skapa en resurs**.

2. Från Azure Marketplace väljer du **compute** > **Windows Server 2019 Data Center**.

3. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. Du skapade det i föregående avsnitt. |
    | **INSTANSINFORMATION** |  |
    | Virtuellt datornamn | Ange *myVm1*. |
    | Region | Välj **USA, östra**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Lämna standard **Windows Server 2019 Data Center**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett användar namn som du väljer. |
    | Lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**. |
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |

4. Välj **Nästa: diskar**.

5. Lämna standardvärdena i **skapa en virtuell dator – diskar**och välj **Nästa: nätverk**.

6. I **Skapa en virtuell dator – nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna kvar standardinställningen **myVirtualNetwork**. |
    | Subnet | Lämna standardinställningen **myVirtualSubnet (10.1.0.0/24)** . |
    | Offentlig IP-adress | Lämna standardinställningen **(ny) myVm-ip**. |
    | Offentliga inkommande portar | Välj **Tillåt valda portar**. |
    | Välj inkommande portar | Välj **HTTP** och **RDP**.

7. Välj **Nästa: hantering**.

8. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

9. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Name | Ange *myvmstorageaccount*. Om det här namnet tas skapar du ett unikt namn.|
    | Typ av konto | Lämna kvar standardinställningen **Lagring (generell användning v1)** . |
    | Prestanda | Lämna kvar standardinställningen **Standard**. |
    | Replikering | Lämna kvar standardinställningen **Lokalt redundant lagring (LRS)** . |

10. Välj **OK**

11. Välj **Granska + skapa**. Du kommer till sidan **Granska + skapa** där Azure verifierar konfigurationen.

12. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

1. Slutför steg 1 och 9 ovan.

    > [!NOTE]
    > För **Virtuellt datornamn** i steg 2 anger du *myVm2*.
    >
    > I steg 7, **Diagnostiklagringskonto**, ska du se till att välja **myvmstorageaccount**.

2. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure verifierar din konfiguration.

3. När du ser meddelandet **valideringen har skickats** väljer du **skapa**.

## <a name="connect-to-a-vm-from-the-internet"></a>Ansluta till en virtuell dator från internet

När du har skapat *myVm1*ansluter du till Internet.

1. I portalens sökfältet anger du *myVm1*.

2. Välj knappen **Anslut**.

    ![Ansluta till en virtuell dator](./media/quick-create-portal/connect-to-virtual-machine.png)

    När du har valt knappen **Anslut** öppnas **Anslut till den virtuella datorn**.

3. Välj **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

4. Öppna den nedladdade *.rdp*-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    2. Ange det användar namn och lösen ord som du angav när du skapade den virtuella datorn.

        > [!NOTE]
        > Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn.

5. Välj **OK**.

6. Du kan få en certifikatvarning under inloggningen. Välj **Ja** eller **Fortsätt** om du får en certifikatvarning.

7. När virtuella datorns skrivbord visas kan du minimera det att gå tillbaka till din lokala dator.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. Öppna PowerShell på fjärrskrivbordet på *myVm1*.

2. Ange `ping myVm2`.

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

3. Ange det efterföljande kommandot för att tillåta *myVm2* att pinga *myVm1* i ett senare skede:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Detta kommando tillåter ICMP-inkommande genom Windows-brandväggen:

4. Stäng fjärrskrivbordsanslutningen till *myVm1*.

5. Utför stegen i [Ansluta till en virtuell dator från Internet](#connect-to-a-vm-from-the-internet) igen, men anslut till *myVm2*.

6. Från en kommandotolk anger du `ping myvm1`.

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

    Du får svar från *myVm1*eftersom du har tillåtit ICMP via Windows-brandväggen på den virtuella datorn *myVm1* i steg 3.

7. Stäng fjärrskrivbordsanslutningen till *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det virtuella nätverket och de virtuella datorerna tar du bort resurs gruppen och alla resurser den innehåller:

1. Skriv *myResourceGroup* i **sökrutan längst** upp i portalen och välj **myResourceGroup** från Sök resultaten.

2. Välj **Ta bort resursgrupp**.

3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett virtuellt standard nätverk och två virtuella datorer. Du är ansluten till en virtuell dator från Internet och kommunicerat på ett säkert sätt mellan de två virtuella datorerna. Läs mer om virtuella nätverksinställningar i [Hantera ett virtuellt nätverk](manage-virtual-network.md).

Som standard tillåter Azure obegränsad säker kommunikation mellan virtuella datorer. I motsats till detta tillåter den endast inkommande fjärrskrivbordsanslutningar till virtuella Windows-datorer från Internet. Mer information om hur du konfigurerar olika typer av nätverkskommunikation för virtuella datorer finns i självstudien [Filtrera nätverkstrafik](tutorial-filter-network-traffic.md).
