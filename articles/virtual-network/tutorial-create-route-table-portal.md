---
title: Dirigera nätverkstrafik – självstudie – Azure-portalen
titlesuffix: Azure Virtual Network
description: I den här självstudien får du lära dig att dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 855adccf036f731de12810fe0f5287186048ddb0
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095965"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Självstudie: Dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen

Som standard dirigerar Azure trafik mellan alla undernät inom ett virtuellt nätverk. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en routningstabell
> * Skapa en väg
> * Skapa ett virtuellt nätverk med flera undernät
> * Associera en routningstabell till ett undernät
> * Skapa en NVA som dirigerar trafik
> * Distribuera virtuella datorer till olika undernät
> * Dirigera trafik från ett undernät till ett annat via en NVA

Om du vill kan du slutföra den här självstudien med hjälp av [Azure CLI](tutorial-create-route-table-cli.md) eller [Azure PowerShell](tutorial-create-route-table-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-route-table"></a>Skapa en routningstabell

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Routningstabell**.

1. I **Skapa routningstabell** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myRouteTablePublic*. |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **Skapa ny**, ange *myResourceGroup* och välj *OK*. |
    | Plats | Låt standardvärdet **USA, östra** vara kvar.
    | BGP-vägspridning | Låt standardvärdet **Aktiverad** vara kvar. |

1. Välj **Skapa**.

## <a name="create-a-route"></a>Skapa en väg

1. I portalens sökfält anger du *myRouteTablePublic*.

1. När **myRouteTablePublic** visas i sökresultatet väljer du det.

1. I **myRouteTablePublic** går du till **Inställningar** och väljer **Vägar** > **+ Lägg till**.

    ![Lägga till väg](./media/tutorial-create-route-table-portal/add-route.png)

1. I **Lägg till väg** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Vägnamn | Ange *ToPrivateSubnet*. |
    | Adressprefix | Ange *10.0.1.0/24*. |
    | Nexthop-typ | Välj **Virtuell installation**. |
    | Nexthop-adress | Ange *10.0.2.4*. |

1. Välj **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan associera en routningstabell till ett undernät måste du skapa ett virtuellt nätverk och ett undernät.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myVirtualNetwork*. |
    | Adressutrymme | Ange *10.0.0.0/16*. |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj ***Välj befintlig*** > **myResourceGroup**. |
    | Plats | Låt standardvärdet **USA, östra** vara kvar. |
    | Undernät – Namn | Ange *Offentligt*. |
    | Undernät – adressintervall | Ange *10.0.0.0/24*. |

1. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="add-subnets-to-the-virtual-network"></a>Lägga till undernät i det virtuella nätverket

1. I portalens sökfält anger du *myVirtualNetwork*.

1. När **myVirtualNetwork** visas i sökresultatet väljer du det.

1. I **myVirtualNetwork** går du till **Inställningar** och väljer **Undernät** > **+ Undernät**.

    ![Lägga till undernät](./media/tutorial-create-route-table-portal/add-subnet.png)

1. I **Lägg till undernät** anger du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *Privat*. |
    | Adressutrymme | Ange *10.0.1.0/24*. |

1. Lämna resten av standardinställningarna och välj **OK**.

1. Välj **+ Undernät** igen. Den här gången anger du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *DMZ*. |
    | Adressutrymme | Ange *10.0.2.0/24*. |

1. Likt förra gången lämnar du resten av standardinställningarna och väljer **OK**.

    Azure visar tre undernät: **Offentligt**, **Privat** och **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associera myRouteTablePublic med ditt undernät Offentligt

1. Välj **Offentligt**.

1. I **Offentligt** väljer du **Routningstabell** > **MyRouteTablePublic** > **Spara**.

    ![Associera routningstabellen](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Skapa en NVA

NVA:er är virtuella datorer som hjälper till med nätverksfunktioner såsom routning och brandväggsoptimering. Du kan välja ett annat operativsystem om du vill. Den här självstudien förutsätter att du använder **Windows Server 2016 Datacenter**.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 Datacenter**.

1. I **Skapa en virtuell dator – grunder** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | **PROJEKTINFORMATION** | |
    | Prenumeration | Välj din prenumeration. |
    | Resursgrupp | Välj **myResourceGroup**. |
    | **INSTANSINFORMATION** |  |
    | Namn på virtuell dator | Ange *myVmNva*. |
    | Region | Välj **USA, östra**. |
    | Alternativ för tillgänglighet | Lämna kvar standardinställningen **Ingen infrastrukturredundans krävs**. |
    | Bild | Lämna kvar standardinställningen **Windows Server 2016 Datacenter**. |
    | Storlek | Lämna kvar standardinställningen **Standard DS1 v2**. |
    | **ADMINISTRATÖRSKONTO** |  |
    | Användarnamn | Ange ett valfritt användarnamn. |
    | Lösenord | Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Bekräfta lösenord | Ange lösenordet igen. |
    | **REGLER FÖR INKOMMANDE PORTAR** |  |
    | Offentliga inkommande portar | Lämna kvar standardinställningen **Ingen**.
    | **SPARA PENGAR** |  |
    | Har du redan en Windows-licens? | Lämna kvar standardinställningen **Nej**. |

1. Välj **Nästa: Diskar**.

1. I **Skapa en virtuell dator – Diskar** väljer du de inställningar som passar dina behov.

1. Välj **Nästa: Nätverk**.

1. I **Skapa en virtuell dator – Nätverk** väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Virtuellt nätverk | Lämna kvar standardinställningen **myVirtualNetwork**. |
    | Undernät | Välj **DMZ (10.0.2.0/24)**. |
    | Offentlig IP-adress | Välj **Ingen**. Du behöver inte en offentlig IP-adress. Den virtuella datorn ansluter inte via Internet.|

1. Lämna resten av standardinställningarna och välj **Nästa: Hantering**.

1. I **Skapa en virtuell dator – Hantering** går du till **Diagnostiklagringskonto** och väljer **Skapa nytt**.

1. I **Skapa lagringskonto** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *mynvastorageaccount*. |
    | Typ av konto | Lämna kvar standardinställningen **Lagring (generell användning v1)**. |
    | Prestanda | Lämna kvar standardinställningen **Standard**. |
    | Replikering | Lämna kvar standardinställningen **Lokalt redundant lagring (LRS)**.

1. Välj **OK**

1. Välj **Granska + skapa**. Du tas till sidan **Granska + skapa** och Azure verifierar din konfiguration.

1. När du ser **Valideringen godkänd** väljer du **Skapa**.

    Det tar några minuter att skapa den virtuella datorn. Fortsätt inte förrän Azure har är färdig med att skapa den virtuella datorn. Sidan **Distributionen är på gång** visar distributionsinformation.

1. När den virtuella datorn är klar väljer du **Gå till resurs**.

## <a name="turn-on-ip-forwarding"></a>Aktivera IP-vidarebefordran

Aktivera IP-vidarebefordran för *myVmNva*. När Azure skickar nätverkstrafik till *myVmNva* gäller att om trafiken är avsedd för en annan IP-adress så skickar IP-vidarebefordran trafiken till rätt plats.

1. På **myVmNva** går du till **Inställningar** och väljer **Nätverk**.

1. Välj **myvmnva123**. Det är det nätverksgränssnitt som Azure skapade för den virtuella datorn. Där finns en sträng med siffror som gör det unikt.

    ![VM-nätverk](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Under **Inställningar** väljer du **IP-konfigurationer**.

1. På **myvmnva123 – IP-konfigurationer** går du till **IP-vidarebefordran** och väljer **Aktiverat**. Välj sedan **Spara**.

    ![Aktivera IP-vidarebefordran](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Skapa offentliga och privata virtuella datorer

Skapa en offentlig virtuell dator och en privat virtuell dator i det virtuella nätverket. Senare använder du dem för att se att Azure dirigerar trafik för det *offentliga* undernätet till det *privata* undernätet via NVA.

Slutför steg 1–12 i [Skapa en NVA](#create-an-nva). Använd de flesta av samma inställningar. Det här är de värden som måste vara olika:

| Inställning | Värde |
| ------- | ----- |
| **OFFENTLIG VIRTUELL DATOR** | |
| GRUNDLÄGGANDE |  |
| Namn på virtuell dator | Ange *myVmPublic*. |
| NÄTVERK | |
| Undernät | Välj **Offentligt (10.0.0.0/24)**. |
| Offentlig IP-adress | Acceptera standardinställningarna. |
| Offentliga inkommande portar | Välj **Tillåt valda portar**. |
| Välj inkommande portar | Välj **HTTP** och **RDP**. |
| HANTERING | |
| Lagringskonto för diagnostik | Lämna kvar standardvärdet **mynvastorageaccount**. |
| **PRIVAT VIRTUELL DATOR** | |
| GRUNDLÄGGANDE |  |
| Namn på virtuell dator | Ange *myVmPrivate*. |
| NÄTVERK | |
| Undernät | Välj **Privat (10.0.1.0/24)**. |
| Offentlig IP-adress | Acceptera standardinställningarna. |
| Offentliga inkommande portar | Välj **Tillåt valda portar**. |
| Välj inkommande portar | Välj **HTTP** och **RDP**. |
| HANTERING | |
| Lagringskonto för diagnostik | Lämna kvar standardvärdet **mynvastorageaccount**. |

Du kan skapa den virtuella datorn *myVmPrivate* medan Azure skapar den virtuella datorn *myVmPublic*. Fortsätt inte med resten av stegen förrän Azure har skapat de båda virtuella datorerna.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logga in på myVmPrivate via fjärrskrivbord

1. I portalens sökfält anger du *myVmPrivate*.

1. När den virtuella datorn **myVmPrivate** visas i sökrutan väljer du det.

1. Välj **Anslut** för att skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmPrivate*.

1. I **Ansluta till den virtuella datorn** väljer du **Ladda ned RDP-fil**. Azure skapar en *.rdp*-fil (Remote Desktop Protocol) och laddar ned den till datorn.

1. Öppna den nedladdade *.rdp*-filen.

    1. Välj **Anslut** om du uppmanas att göra det.

    1. Ange det användarnamn och det lösenord som du angav när du skapade den privata virtuella datorn.

    1. Du kan behöva välja **Fler alternativ** > **Använd ett annat konto** för att använda autentiseringsuppgifterna för den privata virtuella datorn.

1. Välj **OK**.

    Du kan få en certifikatvarning under inloggningsprocessen.

1. Välj **Ja** för att ansluta till den virtuella datorn.

### <a name="enable-icmp-through-the-windows-firewall"></a>Aktivera ICMP via Windows-brandväggen

I ett senare steg använder du vägspårningsverktyget för att testa routningen. Vägspårning använder ICMP (Internet Control Message Protocol), vilket Windows-brandväggen nekar som standard. Aktivera ICMP via Windows-brandväggen.

1. På fjärrskrivbordet för *myVmPrivate* öppnar du PowerShell.

1. Ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Du använder vägspårning för att testa routning i den här självstudien. För produktionsmiljöer rekommenderar vi inte att du tillåter ICMP via Windows-brandväggen.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Aktivera IP-vidarebefordran i myVmNva

Du [aktiverade IP-vidarebefordran](#turn-on-ip-forwarding) för den virtuella datorns nätverksgränssnitt med hjälp av Azure. Den virtuella datorns operativsystem behöver också vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordran för operativsystemet för den virtuella datorn *myVmNva* med följande kommandon.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* öppnar du ett fjärrskrivbord till den virtuella datorn *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Från PowerShell på *myVmNva* anger du följande kommando för att aktivera IP-vidarebefordran:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Starta om den virtuella datorn *myVmNva*. I verktygsfältet väljer du **Start-knappen** > **Strömknappen**, **Annat (planerat)** > **Fortsätt**.

    Detta kopplar även från fjärrskrivbordssessionen.

1. När den virtuella datorn *myVmNva* har startat om skapar du en fjärrskrivbordssession till den virtuella datorn *myVmPublic*. När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* öppnar du en kommandotolk och kör följande kommando:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. På fjärrskrivbordet för *myVmPublic* öppnar du PowerShell.

1. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

    Du kan se att det första hoppet är 10.0.2.4. Det är NVA:ns privata IP-adress. Det andra hoppet är till den privata IP-adressen för den virtuella datorn *myVmPrivate*: 10.0.1.4. Tidigare lade du till vägen till vägtabellen *myRouteTablePublic* och associerade den till det *offentliga* undernätet. Därför skickade Azure trafiken via NVA och inte direkt till det *privata* undernätet.

1. Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.

1. Från en kommandotolk på den virtuella datorn *myVmPrivate* anger du följande kommando:

    ```cmd
    tracert myVmPublic
    ```

    Det testar routningen av nätverkstrafik från den virtuella datorn *myVmPrivate* till den virtuella datorn *myVmPublic*. Svaret liknar det här exemplet:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Du ser att Azure routar trafik direkt från den virtuella datorn *myVmPrivate* till den virtuella datorn *myVmPublic*. Som standard dirigerar Azure trafik direkt mellan undernät.

1. Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla dess resurser när de inte längre behövs:

1. I portalens sökfält anger du *myResourceGroup*.

1. När du ser **myResourceGroup** i sökresultatet väljer du den.

1. Välj **Ta bort resursgrupp**.

1. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en routningstabell och associerat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Nu när du vet hur du gör kan du distribuera olika förkonfigurerade NVA:er från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). De utför många användbara nätverksfunktioner. Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).

Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men Azure kan inte distribuera resurser för vissa PaaS-tjänster till ett virtuellt nätverk. Det är möjligt att begränsa åtkomst till resurserna för vissa Azure PaaS-tjänster. Begränsningen måste dock endast vara trafik från ett undernät för ett virtuellt nätverk. Om du vill lära dig mer om att begränsa nätverksåtkomsten till Azure PaaS-resurser går du vidare till nästa självstudie.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources.md)
