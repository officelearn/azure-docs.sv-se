---
title: Dirigera nätverkstrafik - Azure-portalen | Microsoft Docs
description: Lär dig mer om att dirigera nätverkstrafik till en routingtabell som använder Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 980cf7b59ed16778bbb6cd1b657e3522407c79c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Dirigera nätverkstrafik till en routingtabell som använder Azure portal

Azure automatiskt vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan skapa egna flöden om du vill åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbart om du exempelvis vill vidarebefordra trafik mellan undernät via en virtuell nätverksenhet (NVA). I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en routingtabell
> * Skapa en väg
> * Skapa ett virtuellt nätverk med flera undernät
> * Associera en routingtabell till ett undernät
> * Skapa en NVA som dirigerar trafik
> * Distribuera virtuella datorer (VM) i olika undernät
> * Vidarebefordra trafik från ett undernät till en annan genom en NVA

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-portalen på http://portal.azure.com.

## <a name="create-a-route-table"></a>Skapa en routingtabell

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **routningstabellen**.
3. Ange, eller Välj följande information, acceptera standardvärdet för inställningen återstående och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myRouteTablePublic|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp | Välj **Skapa nytt** och ange *myResourceGroup*.|
    |Plats|Östra USA|
 
    ![Skapa routningstabellen](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Skapa en väg

1. I den *söka efter resurser, tjänster och dokumenten* överst i portalen och börja skriva *myRouteTablePublic*. När **myRouteTablePublic** visas i sökresultaten väljer den.
2. Under **inställningar**väljer **vägar** och välj sedan **+ Lägg till**som visas i följande bild:

    ![Lägg till väg](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. Under **Lägg till väg**, anger, eller Välj följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Vägnamnet|ToPrivateSubnet|
    |Adressprefix| 10.0.1.0/24|
    |Nexthop-typ | Välj **virtuell installation**.|
    |Nexthop-adress| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routingtabell till ett undernät

Innan du kan koppla en routingtabell till ett undernät, måste du skapa ett virtuellt nätverk och undernät och sedan kan du koppla routningstabellen till ett undernät:

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Under **skapa virtuellt nätverk**, anger, eller Välj följande information, acceptera standardinställningarna för de återstående inställningarna och välj sedan **skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork|
    |Adressutrymme| 10.0.0.0/16|
    |Prenumeration | Välj din prenumeration.|
    |Resursgrupp|Välj **använda befintliga** och välj sedan **myResourceGroup**.|
    |Plats|Välj *östra USA*|
    |Namn på undernät|Offentligt|
    |Adressintervall|10.0.0.0/24|
    
4. I den **söka efter resurser, tjänster och dokumenten** överst i portalen och börja skriva *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultaten väljer den.
5. Under **inställningar**väljer **undernät** och välj sedan **+ undernät**som visas i följande bild:

    ![Lägga till undernät](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Välj eller ange följande information och sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|Privat|
    |Adressutrymme| 10.0.1.0/24|

7. Slutför steg 5 och 6 igen, vilket ger följande information:

    |Inställning|Värde|
    |---|---|
    |Namn|DMZ|
    |Adressutrymme| 10.0.2.0/24|

8. Den **myVirtualNetwork - undernät** visas när du har slutfört föregående steg. Under **inställningar**väljer **undernät** och välj sedan **offentliga**.
9. Som det visas i följande bild, Välj **routningstabellen**väljer **MyRouteTablePublic**, och välj sedan **spara**:

    ![Associera routningstabellen](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Skapa en NVA

En NVA är en virtuell dator som utför en funktion i nätverket, till exempel routning, firewalling eller WAN-optimering.

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men stegen förutsätter att du har valt **Windows Server 2016 Datacenter**. 
3. Välj eller ange följande information för **grunderna**och välj **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVmNva|
    |Användarnamn|Ange ett användarnamn som du väljer.|
    |Lösenord|Ange ett lösenord som du väljer. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **använda befintliga** och välj sedan *myResourceGroup*.|
    |Plats|Välj **östra USA**.|
4. Välj en VM-storlek under **välja en storlek**.
5. Välj eller ange följande information för **inställningar**och välj **OK**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk|myVirtualNetwork - om det inte är markerat, Välj **för virtuella nätverk**och välj **myVirtualNetwork** under **Välj virtuellt nätverk**.|
    |Undernät|Välj **undernät** och välj sedan **DMZ** under **Välj undernät**. |
    |Offentlig IP-adress| Välj **offentliga IP-adressen** och välj **ingen** under **Välj offentlig IP-adress**. Ingen offentlig IP-adress har tilldelats den här virtuella datorn eftersom den inte är ansluten till från Internet.
6. Under **skapa** i den **sammanfattning**väljer **skapa** att starta VM-distributionen.

    Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och öppnar en ruta med information om den virtuella datorn.

7. I rutan öppnas för den virtuella datorn när den skapades, under **inställningar**väljer **nätverk**, och välj sedan **myvmnva158** (nätverksgränssnittet Azure som skapats för din Virtuell dator har ett annat antal efter **myvmnva**), enligt följande bild:

    ![VM-nätverk](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. För ett nätverksgränssnitt för att kunna vidarebefordra trafik skickas till den, som inte är avsett för sin egen IP-adressen, måste IP-vidarebefordran aktiveras för nätverksgränssnittet. Under **inställningar**väljer **IP-konfigurationer**väljer **aktiverad** för **IP-vidarebefordring**, och välj sedan **spara** som visas i följande bild:

    ![Aktivera IP-vidarebefordring](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan validera trafiken från den *offentliga* undernät dirigeras till den *privata* undernätet via en NVA i ett senare steg. Slutför steg 1 – 6 för [skapa en NVA](#create-a-network-virtual-appliance). Använd samma inställningar i steg 3 och 5, förutom följande ändringar:

|Namn på virtuell dator      |Undernät      | Offentlig IP-adress     |
|--------- | -----------|---------              |
| myVmPublic  | Offentligt     | Acceptera standardvärdet för portal |
| myVmPrivate | Privat    | Acceptera standardvärdet för portal |

Du kan skapa den *myVmPrivate* VM medan Azure skapar den *myVmPublic* VM. Fortsätt inte med följande steg tills Azure har skapat båda VM: ar.

## <a name="route-traffic-through-an-nva"></a>Vidarebefordra trafik via en NVA

1. I den *Sök* överst i portalen och börja skriva *myVmPrivate*. När den **myVmPrivate** VM visas i sökresultaten markerar du den.
2. Skapa en fjärrskrivbordsanslutning till den *myVmPrivate* VM genom att välja **Anslut**som visas i följande bild:

    ![Ansluta till virtuell dator ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Öppna den hämta RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut**.
4. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapade den virtuella datorn), Välj sedan **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen.
6. Kommandot tracert.exe används i ett senare steg att testa routning. Tracert använder den kontrollen meddelandet ICMP (Internet Protocol), som nekas via Windows-brandväggen. Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Även om tracert används för att testa routning i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.
7. Du har aktiverat IP-vidarebefordran i Azure för den Virtuella datorns nätverksgränssnitt i [aktivera IP-fowarding](#enable-ip-forwarding). I den virtuella datorn, måste operativsystemet eller ett program som körs på den virtuella datorn också kunna vidarebefordra nätverkstrafik. Aktivera IP-vidarebefordring i operativsystemet på den *myVmNva* VM genom att slutföra följande steg från den *myVmPrivate* VM:

    Fjärrskrivbord till den *myVmNva* med följande kommando från en kommandotolk:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Ange följande kommando för att aktivera IP-vidarebefordring i operativsystemet i PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Starta om den virtuella datorn, som också kopplar från fjärrskrivbords-sessionen.
8. När fortfarande är ansluten till den *myVmPrivate* VM, skapa en fjärrskrivbordssession till den *myVmPublic* virtuell dator med följande kommando, när den *myVmNva* VM startas om:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Att testa routning av nätverkstrafik till den *myVmPrivate* virtuell dator från den *myVmPublic* VM, anger du följande kommando från PowerShell:

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
      
    Du kan se att det första hoppet är 10.0.2.4, vilket är en NVA privat IP-adress. Ett andra hopp är 10.0.1.4 privata IP-adressen för den *myVmPrivate* VM. Vägen som lagts till i den *myRouteTablePublic* routningstabellen och som är associerade med den *offentliga* undernät orsakade Azure för att dirigera trafik via en NVA i stället för direkt till den *privata* undernät.
10.  Stäng fjärrskrivbordssession till den *myVmPublic* VM, vilket lämnar du fortfarande är ansluten till den *myVmPrivate* VM.
11. Att testa routning av nätverkstrafik till den *myVmPublic* virtuell dator från den *myVmPrivate* VM, anger du följande kommando från en kommandotolk:

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

    Du kan se trafik vidarebefordras direkt från den *myVmPrivate* så att den *myVmPublic* VM. Som standard vägar för Azure-trafik direkt mellan undernät.
12. Stäng fjärrskrivbordssession till den *myVmPrivate* VM.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller: 

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en enkel NVA som dirigeras trafiken från offentliga undernät till ett privat undernät. Distribuera en mängd olika förkonfigurerade NVAs som utför nätverks-funktioner, till exempel brandvägg och WAN-optimering från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Innan du distribuerar routningstabeller för produktion, rekommenderas att du noggrant bekanta dig med [routning i Azure](virtual-networks-udr-overview.md), [hantera vägtabeller](manage-route-table.md), och [Azure begränsar](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


Resurser för vissa Azure PaaS-tjänster kan inte distribueras till ett virtuellt nätverk medan du kan distribuera många Azure-resurser inom ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten till resurser av vissa Azure PaaS-tjänster till trafik från ett undernät för virtuellt nätverk men. Gå vidare till nästa kurs att lära dig att begränsa nätverksåtkomsten till Azure PaaS-resurser.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](tutorial-restrict-network-access-to-resources.md)
