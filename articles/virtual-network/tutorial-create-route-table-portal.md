---
title: Dirigera nätverkstrafik – självstudie – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här självstudien får du lära dig att dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 854bf2d1ed2155290c2ecebd17695049fdd0c8bb
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314291"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Självstudie: Dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen

Som standard dirigerar Azure trafik mellan alla undernät inom ett virtuellt nätverk. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Anpassade vägar är användbara när du till exempel vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en NVA som dirigerar trafik
> * Skapa en routningstabell
> * Skapa en väg
> * Associera en routningstabell till ett undernät
> * Distribuera virtuella datorer till olika undernät
> * Dirigera trafik från ett undernät till ett annat via en NVA

Den här självstudien använder [Azure-portalen](https://portal.azure.com). Du kan också använda [Azure CLI](tutorial-create-route-table-cli.md) eller [Azure PowerShell](tutorial-create-route-table-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-nva"></a>Skapa en NVA

Virtuella nätverksinstallationer (NVA) är virtuella datorer som hjälper till med nätverksfunktioner, till exempel routning och brandväggsoptimering. Den här självstudien förutsätter att du använder **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem om du vill.

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

1. Välj **Security** > **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter, Skapa en virtuell dator, Azure-portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Ange eller välj den här informationen under **Grunderna**på sidan **Skapa en virtuell dator:**

    | Section | Inställning | Åtgärd |
    | ------- | ------- | ----- |
    | **Projektinformation** | Prenumeration | Välj din prenumeration. |
    | | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj **OK**. |
    | **Information om instans** | Namn på virtuell dator | Ange *myVmNva*. |
    | | Region | Välj **(USA) Östra USA**. |
    | | Alternativ för tillgänglighet | Välj **Ingen infrastrukturredundans krävs**. |
    | | Bild | Välj **Windows Server 2016 Datacenter**. |
    | | Storlek | Behåll standardvärdet Standard **DS1 v2**. |
    | **Administratörskonto** | Användarnamn | Ange ett valfritt användarnamn. |
    | | lösenord | Ange ett lösenord som du väljer, som måste vara minst 12 tecken långt och uppfylla de [definierade komplexitetskraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Bekräfta lösenord | Ange lösenordet igen. |
    | **Regler för inkommande port** | Offentliga inkommande portar | Välj **Ingen**. |
    | **Spara pengar** | Har du redan en Windows Server-licens? | Välj **nej.** |

    ![Grunderna, Skapa en virtuell dator, Azure-portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Välj sedan **Nästa: Diskar >**.

1. Under **Diskar**väljer du de inställningar som är rätt för dina behov och väljer sedan **Nästa: Nätverk >**.

1. Under **Nätverkande:**

    1. För **virtuellt nätverk**väljer du **Skapa nytt**.
    
    1. Ange *myVirtualNetwork*under **Namn**i dialogrutan **Skapa virtuellt nätverk** .

    1. Ersätt det befintliga adressintervallet med *10.0.0.0/16*i **adressutrymmet**.

    1. I **Undernät**markerar du ikonen **Ta bort** för att ta bort det befintliga undernätet och anger sedan följande kombinationer av **undernätsnamn** och **adressintervall**. När ett giltigt namn och område har angetts visas en ny tom rad under den.

        | Namn på undernät | Adressintervall |
        | ----------- | ------------- |
        | *Offentlig* | *10.0.0.0/24* |
        | *Privat* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Välj **OK** om du vill avsluta dialogrutan.

    1. I **Undernät**väljer du **DMZ (10.0.2.0/24)**.

    1. I **Offentlig IP**väljer du **Ingen**, eftersom den här virtuella datorn inte ansluter via internet.

    1. Välj **Nästa : Hantering >**.

1. Under **ledning:**

    1. Välj **Skapa nytt**i **lagringskonto för diagnostik**.
    
    1. Ange eller välj den här informationen i dialogrutan **Skapa lagringskonto:**

        | Inställning | Värde |
        | ------- | ----- |
        | Namn | *mynvastorageaccount* |
        | Typ av konto | **Lagring (allmänt ändamål v1)** |
        | Prestanda | **Standard** |
        | Replikering | **Lokalt redundant lagring (LRS)** |
    
    1. Välj **OK** om du vill avsluta dialogrutan.

    1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure validerar din konfiguration.

1. När meddelandet **Validering har skickats** väljer du **Skapa**.

    Det tar några minuter att skapa den virtuella datorn. Vänta tills Azure har skapat den virtuella datorn. På sidan **Din distribution pågår** visas distributionsinformation.

1. När den virtuella datorn är klar väljer du **Gå till resurs**.

## <a name="create-a-route-table"></a>Skapa en routningstabell

1. På [Azure-portalmenyn](https://portal.azure.com) eller på **startsidan** väljer du **Skapa en resurs**.

2. I sökrutan anger du *rutttabell*. När **rutttabellen** visas i sökresultaten markerar du den.

3. På sidan **Rutttabell** väljer du **Skapa**.

4. I **Skapa routningstabell** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | *myRouteTablePublic* |
    | Prenumeration | Din prenumeration |
    | Resursgrupp | **myResourceGroup** |
    | Location | **Jag är mycket bra på att vi måste ta tid på Östra USA** |
    | Förökning av dirigerar den virtuella nätverksgatewayen | **Enabled** |

    ![Skapa flödestabell, Azure-portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Välj **Skapa**.

## <a name="create-a-route"></a>Skapa en väg

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera din rutttabell. Sök efter och välj **Rutttabeller**.

1. Välj namnet på din rutttabell **(myRouteTablePublic**).

1. Välj **Rutter** > **Lägg till**.

    ![Lägg till rutt, vägtabell, Azure-portal](./media/tutorial-create-route-table-portal/add-route.png)

1. I **Lägg till väg** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Vägnamn | *ToPrivateSubnet* |
    | Adressprefix | *10.0.1.0/24* (adressintervallet för det *privata* undernätet som skapats tidigare) |
    | Nexthop-typ | **Virtuell installation** |
    | Nexthop-adress | *10.0.2.4* (en adress inom *dmz-undernätets* adressintervall) |

1. Välj **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera ditt virtuella nätverk. Sök efter och välj **Virtuella nätverk**.

1. Välj namnet på ditt virtuella nätverk **(myVirtualNetwork).**

1. I det virtuella nätverkets menyrad väljer du **Undernät**.

1. Välj **Offentlig**i den virtuella nätverkss undernätslista .

1. I **tabellen Rutt**väljer du den vägtabell som du skapade **(myRouteTablePublic**) och väljer sedan **Spara** om du vill koppla *flödestabellen* till det offentliga undernätet.

    ![Associera flödestabell, undernätslista, virtuellt nätverk, Azure-portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Aktivera IP-vidarebefordran

Därefter aktiverar du IP-vidarebefordran för din nya virtuella NVA-dator, *myVmNva*. När Azure skickar nätverkstrafik till *myVmNva*, om trafiken är avsedd för en annan IP-adress, skickar IP-vidarebefordran trafiken till rätt plats.

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera din virtuella dator. Sök efter och välj **Virtuella datorer**.

1. Välj namnet på din virtuella dator **(myVmNva).**

1. Välj **Nätverk**i den virtuella datorns menyrad för NVA.

1. Välj **myvmnva123**. Det är det nätverksgränssnitt som Azure skapade för den virtuella datorn. Azure lägger till nummer för att säkerställa ett unikt namn.

    ![Nätverk, virtuell nätverksinstallation (NVA) virtuell dator (VM), Azure-portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Välj **IP-konfigurationer**i menyraden för nätverksgränssnitt .

1. På sidan **IP-konfigurationer** ställer du in **IP-vidarebefordran** till **Aktiverad**och väljer **Spara**.

    ![Aktivera IP-vidarebefordran, IP-konfigurationer, nätverksgränssnitt, virtuell nätverksinstallation (NVA) virtuell dator (VM), Azure-portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Skapa offentliga och privata virtuella datorer

Skapa en offentlig virtuell dator och en privat virtuell dator i det virtuella nätverket. Senare använder du dem för att se att Azure dirigerar trafik för det *offentliga* undernätet till det *privata* undernätet via NVA.

Så här skapar du den offentliga virtuella datorn och den privata virtuella datorn genom [att skapa en NVA](#create-an-nva) tidigare. Du behöver inte vänta på att distributionen ska slutföras eller gå till vm-resursen. Du använder de flesta av samma inställningar, förutom vad som beskrivs nedan.

Innan du väljer **Skapa** för att skapa den offentliga eller privata virtuella datorn går du till följande två underavsnitt ([Offentlig virtuell dator](#public-vm) och privat virtuell [dator](#private-vm)), som visar de värden som måste vara olika. Du kan fortsätta till nästa avsnitt[(Dirigera trafik genom en NVA)](#route-traffic-through-an-nva)när Azure har distribuerat båda virtuella datorerna.

### <a name="public-vm"></a>Offentlig virtuell dator

| Flik | Inställning | Värde |
| --- | ------- | ----- |
| Grundläggande inställningar | Resursgrupp | **myResourceGroup** |
| | Namn på virtuell dator | *myVmPublic* |
| | Offentliga inkommande portar | **Tillåt markerade portar** |
| | Välj inkommande portar | **RDP** |
| Nätverk | Virtuellt nätverk | **myVirtualNetwork** |
| | Undernät | **Offentlig (10.0.0.0/24)** |
| | Offentlig IP-adress | Standardvärdet |
| Hantering | Lagringskonto för diagnostik | **mynvastorageaccount** |

### <a name="private-vm"></a>Privat virtuell dator

| Flik | Inställning | Värde |
| --- | ------- | ----- |
| Grundläggande inställningar | Resursgrupp | **myResourceGroup** |
| | Namn på virtuell dator | *myVmPrivate* |
| | Offentliga inkommande portar | **Tillåt markerade portar** |
| | Välj inkommande portar | **RDP** |
| Nätverk | Virtuellt nätverk | **myVirtualNetwork** |
| | Undernät | **Privat (10.0.1.0/24)** |
| | Offentlig IP-adress | Standardvärdet |
| Hantering | Lagringskonto för diagnostik | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logga in på myVmPrivate via fjärrskrivbord

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera din privata virtuella dator. Sök efter och välj **Virtuella datorer**.

1. Välj namnet på din privata VM **(myVmPrivate).**

1. I menyraden för virtuella datorer väljer du **Anslut** för att skapa en fjärrskrivbordsanslutning till den privata virtuella datorn.

1. På sidan **Anslut med RDP** väljer du **Hämta RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade *.rdp*-filen. Välj **Anslut** om du uppmanas att göra det. Välj **Fler alternativ** > **Använd ett annat konto**och ange sedan det användarnamn och lösenord som du angav när du skapade den privata virtuella datorn.

1. Välj **OK**.

1. Om du får en certifikatvarning under inloggningsprocessen väljer du **Ja** för att ansluta till den virtuella datorn.

### <a name="enable-icmp-through-the-windows-firewall"></a>Aktivera ICMP via Windows-brandväggen

I ett senare steg använder du vägspårningsverktyget för att testa routningen. Vägspårning använder ICMP (Internet Control Message Protocol), vilket Windows-brandväggen nekar som standard. Aktivera ICMP via Windows-brandväggen.

1. På fjärrskrivbordet för *myVmPrivate* öppnar du PowerShell.

1. Ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Du kommer att använda spårningsvägen för att testa routning i den här självstudien. För produktionsmiljöer rekommenderar vi inte att du tillåter ICMP via Windows-brandväggen.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Aktivera IP-vidarebefordran i myVmNva

Du [aktiverade IP-vidarebefordran](#turn-on-ip-forwarding) för den virtuella datorns nätverksgränssnitt med hjälp av Azure. Den virtuella datorns operativsystem behöver också vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordran för operativsystemet för den virtuella datorn *myVmNva* med följande kommandon.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* öppnar du ett fjärrskrivbord till den virtuella datorn *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Från PowerShell på *den virtuella datorn myVmNva* anger du det här kommandot för att aktivera IP-vidarebefordran:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Starta om *virtuella datorn myVmNva:* Välj**Startkraft**i **Aktivitetsfältet** > , **Övrigt (planerat)** > **Fortsätt**.

    Detta kopplar också bort fjärrskrivbordssessionen.

1. När den virtuella datorn *myVmNva* har startat om skapar du en fjärrskrivbordssession till den virtuella datorn *myVmPublic*. När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* öppnar du en kommandotolk och kör följande kommando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Öppna PowerShell på fjärrskrivbordet *i myVmPublic.*

1. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testa routningen av nätverkstrafik

Först testar vi att dirigera nätverkstrafik från den virtuella datorn *myVmPublic* till den virtuella datorn *myVmPrivate*.

1. Från PowerShell på den virtuella datorn *myVmPublic* anger du följande kommando:

    ```powershell
    tracert myVmPrivate
    ```

    Svaret liknar det här exemplet:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Du kan se det första hoppet är till 10.0.2.4, vilket är NVA: s privata IP-adress. Det andra hoppet är till den privata IP-adressen för *myVmPrivate* VM: 10.0.1.4. Tidigare lade du till vägen till vägtabellen *myRouteTablePublic* och associerade den till det *offentliga* undernätet. Därför skickade Azure trafiken via NVA och inte direkt till det *privata* undernätet.

1. Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* anger du följande kommando:

    ```cmd
    tracert myVmPublic
    ```

    Det här kommandot testar routning av nätverkstrafik från *myVmPrivate* VM till *myVmPublic* VM. Svaret liknar det här exemplet:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Du kan se att Azure dirigerar trafik direkt från *myVmPrivate-datorn* till *myVmPublic-virtuella* datorn. Som standard dirigerar Azure trafik direkt mellan undernät.

1. Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

När resursgruppen inte längre behövs tar du bort *myResourceGroup* och alla resurser som den har:

1. Gå till [Azure-portalen](https://portal.azure.com) för att hantera din resursgrupp. Sök efter och välj **Resursgrupper**.

1. Välj namnet på resursgruppen **(myResourceGroup**).

1. Välj **Ta bort resursgrupp**.

1. I bekräftelsedialogrutan anger du *myResourceGroup* för **TYP RESURSGRUPPNAMN**och väljer sedan **Ta bort**. Azure tar bort *myResourceGroup* och alla resurser som är kopplade till den resursgruppen, inklusive dina flödestabeller, lagringskonton, virtuella nätverk, virtuella datorer, nätverksgränssnitt och offentliga IP-adresser.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en routningstabell och associerat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Nu kan du distribuera olika förkonfigurerade NVA:er från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), som tillhandahåller många användbara nätverksfunktioner. Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men Azure kan inte distribuera resurser för vissa PaaS-tjänster till ett virtuellt nätverk. Det är möjligt att begränsa åtkomsten till resurserna för vissa Azure PaaS-tjänster, men begränsningen får bara vara trafik från ett virtuellt nätverksundernät. Mer information om hur du begränsar nätverksåtkomsten till Azure PaaS-resurser finns i nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources.md)
