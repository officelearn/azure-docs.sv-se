---
title: "Dirigera nätverkstrafik - Azure-portalen | Microsoft Docs"
description: "Lär dig mer om att dirigera nätverkstrafik till en routingtabell som använder Azure portal."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Dirigera nätverkstrafik till en routingtabell som använder Azure portal

Azure automatiskt vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Du kan skapa egna flöden om du vill åsidosätta Azures standardroutning. Möjligheten att skapa anpassade vägar är användbart om du till exempel du vill vidarebefordra trafik mellan undernät via en brandvägg. I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Skapa en routingtabell
> * Skapa en väg
> * Associera en routingtabell till ett undernät för virtuellt nätverk
> * Testa Routning
> * Felsöka routning

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på http://portal.azure.com.

## <a name="create-a-route-table"></a>Skapa en routingtabell

Azure vägar trafik mellan alla undernät i ett virtuellt nätverk som standard. Läs mer om Azures standardvägar i [systemvägar](virtual-networks-udr-overview.md). Om du vill åsidosätta standardvärdet för Azures routning, skapa en routingtabell som innehåller vägar och associera routningstabellen till ett undernät för virtuellt nätverk.

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **routningstabellen**.
3. Välj din **prenumeration** och välj eller ange följande information och sedan **skapa**:
 
    ![Skapa routningstabellen](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Skapa en väg

En routningstabell innehåller noll eller flera vägar. 

1. I den *söka efter resurser, tjänster och dokumenten* överst i portalen och börja skriva *myRouteTablePublic*. När **myRouteTablePublic** visas i sökresultaten väljer den.
2. Under **inställningar**väljer **vägar** och välj sedan **+ Lägg till**som visas i följande bild:

    ![Lägg till väg](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. Under **Lägg till väg**, Välj eller ange följande information och välj sedan **OK**:
    - **Vägnamnet**: *ToPrivateSubnet*
    - **Adressprefixet**: 10.0.1.0/24
    - **Nästa hopptyp**: Välj **virtuell installation**.
    - **Nästa hopp**: 10.0.2.4

    Vägen kommer att dirigera all trafik till adressprefixet 10.0.1.0/24 via en virtuell nätverksenhet med IP-adressen 10.0.2.4. Virtuell nätverksenhet och undernät med det angivna adressprefixet skapas i senare steg. Vägen åsidosätter Azures standard routning, som dirigerar trafik mellan undernät direkt. Varje väg anger nästa hopptyp. Nästa hopptyp instruerar Azure så att dirigera trafiken. I det här exemplet nästa hopptyp är *VirtualAppliance*. Läs mer om alla tillgängliga nästa hopptyper i Azure och när de ska användas i [typer för nästa hopp](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associera en routingtabell till ett undernät

Innan du kan koppla en routingtabell till ett undernät, måste du skapa ett virtuellt nätverk och undernät:

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **nätverk**, och välj sedan **för virtuella nätverk**.
3. Under **skapa virtuellt nätverk**, Välj, eller ange följande information och sedan **skapa**:
    
    - **Name**: *myVirtualNetwork*
    - **Adressutrymmet**: *10.0.0.0/16*
    - **Prenumerationen**: väljer din prenumeration.
    - **Resursgruppen**: Välj **använda befintliga** och välj **myResourceGroup**.
    - **Plats**: Välj *östra USA*
    - **Undernätnamnet**: *offentliga*
    - **Adressintervall:** *10.0.0.0/24*

4. I den **söka efter resurser, tjänster och dokumenten** överst i portalen och börja skriva *myVirtualNetwork*. När **myVirtualNetwork** visas i sökresultaten väljer den.
5. Lägga till två undernät i det virtuella nätverket. Under **inställningar**väljer **undernät** och välj sedan **+ undernät**som visas i följande bild:

    ![Lägga till undernät](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Välj eller ange följande information och sedan **OK**:
    - **Namnet**: privat
    - **Adressutrymmet**: *10.0.1.0/24*

7. Slutför steg 5 och 6 igen, vilket ger följande information:
    - **Namnet**: DMZ
    - **Adressutrymmet**: *10.0.2.0/24*

Du kan associera en routingtabell till noll eller flera undernät. Ett undernät kan ha noll eller en vägtabell associerad till den. Utgående trafik från ett undernät vidarebefordras baserat på Azures standardvägar och eventuella anpassade vägar som du har lagt till en routingtabell som du kopplar till ett undernät. Koppla den *myRouteTablePublic* routningstabellen till den *offentliga* undernät:

1. Den **myVirtualNetwork - undernät** visas när du har slutfört föregående steg. Under **inställningar**väljer **undernät** och välj sedan **offentliga**.
2. Som det visas i följande bild, Välj **routningstabellen**och välj **MyRouteTablePublic**.

    ![Associera routningstabellen](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Välj **Spara**.

## <a name="test-routing"></a>Testa Routning

Om du vill testa routning, ska du skapa en virtuell dator som fungerar som virtuell nätverksenhet att flödet som du skapade i föregående steg vägar via. När du har skapat virtuell nätverksenhet du ska distribuera en virtuell dator i den *offentliga* och *privata* undernät. Du kommer sedan att dirigera trafik från den *offentliga* undernät så att den *privata* undernätet via virtuell nätverksenhet.

### <a name="create-a-network-virtual-appliance"></a>Skapa en virtuell nätverksenhet

I föregående steg skapas en väg som angetts av en virtuell nätverksenhet som nexthop-typen. En virtuell dator som kör ett nätverksprogram kallas ofta för en virtuell nätverksenhet. Den virtuella nätverksenhet som du distribuerar är ofta en förkonfigurerad virtuell dator i produktionsmiljöer. Flera virtuella nätverksinstallationer är tillgängliga från den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). En enkel virtuell dator skapas i den här artikeln.

1. Välj **+ skapa en resurs** i övre vänstra hörnet på Azure-portalen.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men stegen förutsätter att du har valt **Windows Server 2016 Datacenter**. 
3. Välj eller ange följande information för **grunderna**och välj **OK**:
    - **Name**: *myVmNva*
    - **Resursgruppen**: Välj **använda befintliga** och välj sedan *myResourceGroup*.
    - **Plats**: Välj *östra USA*.

    Den **användarnamn** och **lösenord** du anger används i ett senare steg. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Den **plats** och **prenumeration** valt måste vara samma som plats och prenumerationen som det virtuella nätverket. Det är inte nödvändigt att du väljer du samma resursgrupp som det virtuella nätverket har skapats i, men samma resursgrupp som har valts för den här kursen.
4. Välj en VM-storlek under **välja en storlek**.
5. Välj eller ange följande information för **inställningar**och välj **OK**:
    - **Virtuellt nätverk**: se till att **myVirtualNetwork** är markerad. Om inte, Välj **för virtuella nätverk**och välj **myVirtualNetwork** under **Välj virtuellt nätverk**.
    - **Undernät**: Välj **undernät** och välj sedan **DMZ** under **Välj undernät**.
    - **Offentliga IP-adressen**: Välj **offentliga IP-adressen** och välj **ingen** under **Välj offentlig IP-adress**. Ingen offentlig IP-adress har tilldelats den här virtuella datorn eftersom den inte är ansluten till från Internet.
6. Under **skapa** i den **sammanfattning**väljer **skapa** att starta distributionen av virtuella datorer.

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte till nästa steg förrän Azure har skapat den virtuella datorn och öppnar en ruta med information om den virtuella datorn.

När Azure skapade den virtuella datorn, det skapas också en [nätverksgränssnittet](virtual-network-network-interface.md) i den *DMZ* undernät och bifogas nätverksgränssnittet till den virtuella datorn. IP-vidarebefordran måste vara aktiverat för varje Azure nätverksgränssnitt som vidarebefordrar trafik till IP-adresser som inte är tilldelade till nätverksgränssnittet.

1. I rutan öppnas för den virtuella datorn när den skapades, under **inställningar**väljer **nätverk**, och välj sedan **myvmnva158** (nätverksgränssnittet Azure Skapa för den virtuella datorn har ett annat antal efter **myvmnva**), enligt följande bild:

    ![Virtuella nätverk](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    När du skapade virtuell nätverksenhet i den *DMZ* undernät, Azure automatiskt skapade nätverksgränssnittet kopplade nätverksgränssnittet till den virtuella datorn och tilldelas den privata IP-adressen  *10.0.2.4* till nätverksgränssnittet. 

2. Under **inställningar**väljer **IP-konfigurationer**väljer **aktiverad** för **IP-vidarebefordring**, och välj sedan **spara** som visas i följande bild:

    ![Aktivera IP-vidarebefordring](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa två virtuella datorer i det virtuella nätverket så att du kan verifiera att trafik från den *offentliga* undernät dirigeras till den *privata* undernätet via virtuell nätverksenhet i ett senare steg.

Slutför steg 1 – 6 för [skapa en virtuell nätverksenhet](#create-a-network-virtual-appliance). Använd samma inställningar i steg 3 och 5, förutom följande ändringar:

|Virtuell dator   |Namn      |Undernät      | Offentlig IP-adress     |
|---------         |--------- | -----------|---------              |
|Virtuella 1 | myVmWeb  | Offentligt     | Acceptera standardvärdet för portal |
|virtuell dator 2 | myVmMgmt | Privat    | Acceptera standardvärdet för portal |

Du kan skapa den *myVmMgmt* virtuell dator medan Azure skapar den *myVmWeb* virtuella datorn. Fortsätt inte med följande steg tills Azure har skapat både virtuella datorer.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Vidarebefordra trafik via en virtuell nätverksenhet

1. I den *Sök* överst i portalen och börja skriva *myVmMgmt*. När **myVmMgmt** visas i sökresultaten väljer den.
2. Skapa en fjärrskrivbordsanslutning till den *myVmMgmt* virtuell dator genom att välja **Anslut**som visas i följande bild:

    ![Ansluta till den virtuella datorn](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Öppna den hämta RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut**.
4. Ange användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn), välj sedan **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** att fortsätta med anslutningen.
6. Kommandot tracert.exe används i ett senare steg att testa routning. Tracert använder ICMP som nekas via Windows-brandväggen. Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från en kommandotolk:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Även om tracert används för att testa routning i den här artikeln, rekommenderas inte att tillåta ICMP via Windows-brandväggen för Produktionsdistribution.
7. Du har aktiverat IP-vidarebefordran i Azure för den virtuella datorns nätverksgränssnitt i [aktivera IP-fowarding](#enable-ip-forwarding). I den virtuella datorn, måste operativsystemet eller ett program som körs på den virtuella datorn också kunna vidarebefordra nätverkstrafik. När du distribuerar en virtuell nätverksenhet i en produktionsmiljö anordningen vanligtvis filtrerar, loggar eller utför vissa andra funktion innan den vidarebefordrar trafik. I den här artikeln dock vidarebefordrar operativsystemet all trafik som tas emot. Aktivera IP-vidarebefordring i operativsystemet på den *myVmNva* genom att utföra följande steg från den *myVmMgmt* virtuell dator:

    Fjärrskrivbord till den *myVmNva* virtuell dator med följande kommando från en kommandotolk:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Ange följande kommando för att aktivera IP-vidarebefordring i operativsystemet i PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Starta om den virtuella datorn, vilket kommer också att koppla från fjärrskrivbords-sessionen.
8. När fortfarande är ansluten till den *myVmMgmt* virtuella datorn, efter den *myVmNva* omstarter av den virtuella datorn, skapa en fjärrskrivbordssession till den *myVmWeb* virtuell dator med följande kommando:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Aktivera ICMP via Windows-brandväggen genom att skriva följande kommando från en kommandotolk:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. Att testa routning av nätverkstrafik till den *myVmMgmt* virtuell dator från den *myVmWeb* virtuella datorn, anger du följande kommando från en kommandotolk:

    ```
    tracert myvmmgmt
    ```

    Svaret liknar följande exempel:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Du kan se att det första hoppet är 10.0.2.4, vilket är den virtuella nätverksenhets privat IP-adress. Ett andra hopp är 10.0.1.4 privata IP-adressen för den *myVmMgmt* virtuella datorn. Vägen som lagts till i den *myRouteTablePublic* routningstabellen och som är associerade med den *offentliga* undernät orsakade Azure för att dirigera trafik via en NVA i stället för direkt till den *privata* undernät.
10.  Stäng fjärrskrivbordssession till den *myVmWeb* virtuell dator, vilket lämnar du fortfarande är ansluten till den *myVmMgmt* virtuella datorn.
11. Att testa routning av nätverkstrafik till den *myVmWeb* virtuell dator från den *myVmMgmt* virtuella datorn, anger du följande kommando från en kommandotolk:

    ```
    tracert myvmweb
    ```

    Svaret liknar följande exempel:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Du kan se trafik vidarebefordras direkt från den *myVmMgmt* virtuella datorn till den *myVmWeb* virtuella datorn. Som standard vägar för Azure-trafik direkt mellan undernät.
12. Stäng fjärrskrivbordssession till den *myVmMgmt* virtuella datorn.

## <a name="troubleshoot-routing"></a>Felsöka routning

Du har lärt dig i föregående steg gäller standardvägar som du kan alternativt kan du åsidosätta med egna vägar i Azure. Ibland kan trafik inte dirigeras som du tror att. Du kan använda den [nästa hopp](../network-watcher/network-watcher-check-next-hop-portal.md) möjligheterna för Azure Nätverksbevakaren att avgöra hur Azure är dirigera trafiken mellan två virtuella datorer. 

1. I den *Sök* överst i portalen och börja skriva *Nätverksbevakaren*. När **Nätverksbevakaren** visas i sökresultaten väljer den.
2. Under **verktyg för NÄTVERKSDIAGNOSTIK**väljer **nästa hopp**.
3. Att testa routning av nätverkstrafik från den *myVmWeb* (10.0.0.4) virtuella datorn till den *myVmMgmt* (10.0.1.4) virtuell dator, väljer din prenumeration, ange informationen som visas i följande bild (din **Nätverksgränssnittet** namn skiljer), och välj sedan **nästa hopp**:

    ![Nästa hopp](./media/tutorial-create-route-table-portal/next-hop.png)  

    Den **resultatet** utdata informerar dig om att nästa hopp-IP-adress för trafik från *myVmWeb* till *myVmMgmt* är 10.0.2.4 (den *myVmNva* virtuell dator), att nästa hopptyp är *VirtualAppliance*, och att routningstabellen som orsakar routning är *myRouteTablePublic*.

Effektiva vägar för varje nätverksgränssnitt är en kombination av Azures standardvägar och alla vägar som du definierar. Om du vill se alla vägar som gäller för ett nätverksgränssnitt i en virtuell dator, gör du följande:

1. I den *Sök* överst i portalen och börja skriva *myVmWeb*. När **myVmWeb** visas i sökresultaten väljer den.
2. Under **inställningar**väljer **nätverk**, och välj sedan **myvmweb369** (nätverksgränssnittet Azure skapas för den virtuella datorn har ett annat antal efter **myvmweb**).
3. Under **stöd + felsökning**väljer **effektiva vägar**som visas i följande bild:

    ![Effektiva vägar](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Du kan se Azures standardvägar och flödet som du lade till i den *myRouteTablePublic* routningstabellen. Läs mer om hur Azure väljs en väg i listan över vägar i [hur Azure väljs en väg](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen och alla resurser som den innehåller: 

1. Ange *myResourceGroup* i den **Sök** överst i portalen. När du ser **myResourceGroup** i sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Ange *myResourceGroup* för **typ av RESURSGRUPPENS namn:** och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här artikeln, skapa en routingtabell och som är kopplad till ett undernät. Du har skapat en virtuell nätverksenhet som dirigeras trafiken från offentliga undernät till ett privat undernät. Resurser för vissa Azure PaaS-tjänster kan inte distribueras till ett virtuellt nätverk medan du kan distribuera många Azure-resurser inom ett virtuellt nätverk. Du kan fortfarande begränsa åtkomsten till resurser av vissa Azure PaaS-tjänster till trafik från ett undernät för virtuellt nätverk men. Gå vidare till nästa kurs att lära dig att begränsa nätverksåtkomsten till Azure PaaS-resurser.

> [!div class="nextstepaction"]
> [Begränsa nätverksåtkomst till PaaS-resurser](virtual-network-service-endpoints-configure.md#azure-portal)
