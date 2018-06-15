---
title: Dirigera nätverkstrafik – självstudie – Azure-portalen | Microsoft Docs
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
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 7254e9336fca14daee2021d5bde4c5538509fe35
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842330"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Självstudie: Dirigera nätverkstrafik med en routningstabell med hjälp av Azure-portalen

Azure dirigerar automatiskt trafik mellan alla undernät inom ett virtuella nätverk som standard. Du kan skapa egna vägar för att åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbar om du exempelvis vill dirigera trafik mellan undernät via en virtuell nätverksinstallation (NVA). I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en routningstabell
> * Skapa en väg
> * Skapa ett virtuellt nätverk med flera undernät
> * Associera en routningstabell till ett undernät
> * Skapa en NVA som dirigerar trafik
> * Distribuera virtuella datorer till olika undernät
> * Dirigera trafik från ett undernät till ett annat via en NVA

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-create-route-table-cli.md) eller [Azure PowerShell](tutorial-create-route-table-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-route-table"></a>Skapa en routningstabell

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och välj därefter **Routningstabell**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myRouteTablePublic|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp | Välj **Skapa ny** och skriv *myResourceGroup*.|
    |Plats|Östra USA|
 
    ![Skapa routningstabell](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Skapa en väg

1. I rutan *Sök efter resurser, tjänster och dokument* högst upp i portalen börjar du skriva *myRouteTablePublic*. När **myRouteTablePublic** visas i sökresultatet väljer du det.
2. Under **INSTÄLLNINGAR** väljer du **Vägar** och sedan **+ Lägg till** som du ser på följande bild:

    ![Lägga till väg](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. Under **Lägg till routning** anger eller väljer du följande information, accepterar standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Vägnamn|ToPrivateSubnet|
    |Adressprefix| 10.0.1.0/24|
    |Nexthop-typ | Välj **Virtuell installation**.|
    |Nexthop-adress| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routningstabell till ett undernät

Innan du kan associera en routningstabell till ett undernät måste du skapa ett virtuellt nätverk och ett undernät. Därefter kan du associera routningstabellen till ett undernät:

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Under **Skapa virtuellt nätverk** anger du eller väljer följande information, accepterar standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork|
    |Adressutrymme| 10.0.0.0/16|
    |Prenumeration | Välj din prenumeration.|
    |Resursgrupp|Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Plats|Välj *USA, östra*|
    |Namn på undernät|Offentligt|
    |Adressintervall|10.0.0.0/24|
    
4. I rutan **Sök efter resurser, tjänster och dokument** högst upp i portalen och börjar du skriva *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultatet väljer du det.
5. Under **INSTÄLLNINGAR** väljer du **Undernät** och väljer sedan **+ Undernät** som du ser på följande bild:

    ![Lägga till undernät](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Välj eller ange följande information och välj **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|Privat|
    |Adressutrymme| 10.0.1.0/24|

7. Utför steg 5 och 6 igen och ange följande information:

    |Inställning|Värde|
    |---|---|
    |Namn|DMZ|
    |Adressutrymme| 10.0.2.0/24|

8. Rutan **myVirtualNetwork - Subnets** (myVirtualNetwork Undernät) visas när du har slutfört föregående steg. Under **INSTÄLLNINGAR** väljer du **Undernät** och sedan **Offentligt**.
9. Som du ser i följande bild ska du välja **Routningstabell**, **MyRouteTablePublic** och sedan **Spara**:

    ![Associera routningstabellen](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en nätverksfunktion, som routning, brandvägg eller WAN-optimering.

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men i återstående steg förutsätts att du har valt **Windows Server 2016 Datacenter**. 
3. Välj eller ange följande information vid **Grundläggande inställningar** och välj **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVmNva|
    |Användarnamn|Ange ett valfritt användarnamn.|
    |Lösenord|Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **Använd befintlig** och sedan *myResourceGroup*.|
    |Plats|Välj **USA, östra**.|
4. Välj en VM-storlek i **Välj en storlek**.
5. Välj eller ange följande information vid **Inställningar** och välj **OK**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk|myVirtualNetwork – Om det inte är valt väljer du **Virtuellt nätverk** och sedan **myVirtualNetwork** under **Välj ett virtuellt nätverk**.|
    |Undernät|Välj **Undernät** och **DMZ** under **Välj undernät**. |
    |Offentlig IP-adress| Välj **Offentlig IP-adress** och sedan **Ingen** under **Välj offentlig IP-adress**. Ingen offentlig IP-adress är tilldelad till den här virtuella datorn eftersom den inte kommer att anslutas till från internet.
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

    Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och en ruta öppnas med information om den virtuella datorn.

7. I rutan som öppnades när den virtuella datorn hade skapats går du till **INSTÄLLNINGAR**, väljer **Nätverk** och sedan **myvmnva158** (nätverksgränssnittet som Azure skapade för din virtuella dator har en annan siffra efter **myvmnva**), enligt bilden nedan:

    ![VM-nätverk](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. För att ett nätverksgränssnitt ska kunna vidarebefordra nätverkstrafik som skickas till det som inte är avsett för den egna IP-adressen måste IP-vidarebefordran vara aktiverat för nätverksgränssnittet. Under **INSTÄLLNINGAR** väljer du **IP-konfigurationer**, sedan **Aktiverat** för **IP-vidarebefordring** och därefter **Spara**, enligt bilden nedan:

    ![Aktivera IP-vidarebefordran](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket, så att du kan verifiera att trafiken från det *offentliga* undernätet dirigeras till det *privata* undernätet via NVA i ett senare steg. Slutför steg 1–6 i [Skapa en NVA](#create-a-network-virtual-appliance). Använd samma inställningar i steg 3 och 5, förutom följande ändringar:

|Namn på virtuell dator      |Undernät      | Offentlig IP-adress     |
|--------- | -----------|---------              |
| myVmPublic  | Offentligt     | Acceptera standardvärden för portalen |
| myVmPrivate | Privat    | Acceptera standardvärden för portalen |

Du kan skapa den virtuella datorn *myVmPrivate* medan Azure skapar den virtuella datorn *myVmPublic*. Fortsätt inte med följande steg förrän Azure har skapat de båda virtuella datorerna.

## <a name="route-traffic-through-an-nva"></a>Dirigera trafik via NVA

1. I rutan *Sök* högst upp i portalen börjar du skriva *myVmPrivate*. När den virtuella datorn **myVmPrivate** visas i sökrutan väljer du det.
2. Skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVmPrivate* genom att välja **Anslut** enligt bilden nedan:

    ![Ansluta till virtuell dator ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Öppna den hämtade RDP-filen för att ansluta till den virtuella datorn. Om det händer väljer du **Anslut**.
4. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
6. Senare används vägspårningsverktyget för att testa routningen. Vägspårning använder ICMP (Internet Control Message Protocol), som definieras via Windows-brandväggen. Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPrivate*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Även om vägspårning används för att testa routning i den här självstudien rekommenderar vi inte att du tillåter att ICMP kommer igenom Windows-brandväggen för produktionsdistribution.
7. Du aktiverade IP-vidarebefordran inom Azure för de virtuella datorernas nätverksgränssnitt i [Aktivera IP-vidarebefordran](#enable-ip-forwarding). I den virtuella datorn måste operativsystemet, eller ett program som körs i den virtuella datorn, kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordring i operativsystemet för den virtuella datorn *myVmNva*:

    Från en kommandotolk i den virtuella datorn *myVmPrivate*, fjärrskrivbord till den virtuella datorn *myVmNva*:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Om du vill aktivera IP-vidarebefordran inom operativsystemet anger du följande kommando i PowerShell från den virtuella datorn *myVmNva*:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Starta om den virtuella datorn *myVmNva*, vilket även kopplar från fjärrskrivbordssessionen.
8. När du fortfarande är ansluten till den virtuella datorn *myVmPrivate* ska du skapa en fjärrskrivbordssession till *myVmPublic*när *myVmNva* har startats om:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Aktivera ICMP via Windows-brandväggen genom att ange följande kommando från PowerShell på den virtuella datorn *myVmPublic*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPrivate* från *myVmPublic* anger du följande kommando från PowerShell på *myVmPublic*:

    ```
    tracert myVmPrivate
    ```

    Svaret liknar följande exempel:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Du kan se att det första hoppet är 10.0.2.4, som är NVA-enhetens privata IP-adress. Det andra hoppet är is 10.0.1.4, som är den privata IP-adressen för den virtuella datorn *myVmPrivate*. Vägen som har lagts till i routningstabellen *myRouteTablePublic* och associerats till det *offentliga* undernätet gjorde så att Azure dirigerade trafiken via NVA istället för direkt till det *privata* undernätet.
10.  Stäng fjärrskrivbordssession för den virtuella datorn *myVmPublic*. Du är fortfarande ansluten till *myVmPrivate*.
11. Om du vill testa att dirigera nätverkstrafik till den virtuella datorn *myVmPublic* från *myVmPrivate* anger du följande kommando från en kommandotolk på *myVmPrivate*:

    ```
    tracert myVmPublic
    ```

    Svaret liknar följande exempel:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Du ser att trafik vidarebefordras direkt från *myVmPrivate* till the *myVmPublic*. Som standard dirigerar Azure trafik direkt mellan undernät.
12. Stäng fjärrskrivbordssessionen för den virtuella datorn *myVmPrivate*.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller: 

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du det.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en routningstabell och associerat den till ett undernät. Du har skapat en enkel NVA som dirigerade trafik från ett offentligt till ett privat undernät. Distribuera en mängd olika förkonfigurerade NVA-enheter som utför nätverksfunktioner som brandvägg och WAN-optimering från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Mer information om routning finns i [routningsöversikten](virtual-networks-udr-overview.md) och [Hantera en routningstabell](manage-route-table.md).


Du kan distribuera många Azure-resurser inom ett virtuellt nätverk, men resurser för vissa Azure PaaS-tjänster går inte att distribuera till ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten för resurserna i vissa Azure PaaS-tjänster till trafik enbart från ett undernät för ett virtuell dator. Om du vill lära dig mer om att begränsa nätverksåtkomsten till Azure PaaS-resurser går du vidare till nästa självstudie.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources.md)
