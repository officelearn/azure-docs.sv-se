---
title: 'Självstudie: Virtuella datorer med lastbalanserare i flera tillgänglighetszoner – Azure Portal'
titlesuffix: Azure Load Balancer
description: Den här kursen visar hur du skapar en Standard Load Balancer med zonredundant klientdel för att lastbalansera virtuella datorer i flera tillgänglighetszoner med hjälp av Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: e83991f553d00af11cfc275137f8e73ebab2098c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882645"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Självstudie: Lastbalansera virtuella datorer i flera tillgänglighetszoner med Standard Load Balancer med hjälp av Azure Portal

Med belastningsutjämning får du högre tillgänglighet genom att inkommande begäranden sprids över flera virtuella datorer. Den här kursen går stegvis igenom hur du skapar en offentlig Standard Load Balancer som lastbalanserar virtuella datorer i flera tillgänglighetszoner. Det här hjälper till att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. Med zonredundans kan en eller flera tillgänglighetszoner misslyckas och datasökvägen överleva så länge en zon i regionen har god status. Lär dig att:

> [!div class="checklist"]
> * Skapa en Standard Load Balancer
> * skapa nätverkssäkerhetsgrupper för att definiera regler för inkommande trafik
> * Skapa zonredundanta virtuella datorer över flera zoner och ansluta till en lastbalanserare
> * skapa hälsoavsökning för lastbalanseraren
> * skapa trafikregler för lastbalanseraren
> * skapa en grundläggande IIS-webbplats
> * visa en lastbalanserare i praktiken

Mer information om hur du använder tillgänglighetszoner med standardlastbalanserare finns i [Standard Load Balancer och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du vill kan du slutföra den här självstudien med [Azure CLI](load-balancer-standard-public-zone-redundant-cli.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress medan du skapar lastbalanseraren konfigureras den automatiskt som en standard-SKU-version och blir också automatiskt zonredundant.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Lastbalanserare**.
2. Ange dessa värden för lastbalanseraren på sidan **Skapa en lastbalanserare**:
    - *myLoadBalancer* – namnet på lastbalanseraren.
    - **Public** – typen av lastbalanseraren.
     - *myPublicIP* – för den nya offentliga IP-adress som du skapar. Om du vill göra det klickar du på **Välj en offentlig IP-adress** och klickar sedan på **Skapa ny**. För namntypen *myPublicIP* är SKU är standard och väljer **Zonredundant** som **tillgänglighetszon**.
    - *myResourceGroupLBAZ* – för namnet på den nya resursgrupp som du skapar.
    - **westeurope** – för platsen.
3. Skapa lastbalanseraren genom att klicka på **Skapa**.
   
    ![Skapa en lastbalanserare](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, virtuella datorer i olika zoner för regionen och installerar sedan IIS på de virtuella datorerna för att testa den zonredundanta lastbalanseraren. Om en zon misslyckas så misslyckas därför hälsoavsökningen för virtuell dator i samma zon och trafiken fortsätter att fungera med virtuella datorer i andra zoner.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk för distribution av serverdelsservrar.

1. Klicka på **Skapa en resurs** > **Nätverk** > **Virtuella nätverk** överst till vänster på skärmen och ange följande värden för det virtuella nätverket:
    - *myVnet* – för det virtuella nätverkets namn.
    - *myResourceGroupLBAZ* – för namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

    ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

Skapa en nätverkssäkerhetsgrupp så att du kan definiera inkommande anslutningar till det virtuella nätverket.

1. Högst upp till vänster på skärmen klickar du på **Skapa en resurs**, i sökrutan skriver du *Nätverkssäkerhetsgrupp*, och på nätverkssäkerhetsgruppens sidan klickar du på **Skapa**.
2. Ange dessa värden på sidan Skapa nätverkssäkerhetsgrupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLB* – för namnet på den befintliga resursgruppen.
   
![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Skapa regler för nätverkssäkerhetsgrupp

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar använder HTTP och RDP med Azure Portal.

1. I Azure Portal klickar du på **Alla resurser** i den vänstra menyn. Sök sedan och klicka på **myNetworkSecurityGroup** som finns i resursgruppen **myResourceGroupLBAZ**.
2. Klicka på **Ingående säkerhetsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för lastbalanseringsregelns namn.
    - *Tillåt HTTP* – för beskrivning av lastbalanseringsregeln.
4. Klicka på **OK**.
 
 ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Skapa, genom att upprepa steg 2 till 4, en annan regel med namnet *myRDPRule* som tillåter att en inkommande RDP-anslutning använder port 3389 med följande värden:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *3389* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *200* för **Prioritet**
    - *myRDPRule* för namn
    - *Tillåt HTTP* – för beskrivning

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa virtuella datorer i olika zoner (zon 1, zon 2 och zon 3) för den region som kan fungera som serverdelsservrar för lastbalanseraren.

1. Klicka på **Skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter** överst till vänster på skärmen och ange följande värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.    
    - *myResourceGroupLBAZ* – för **Resursgrupp**väljer du **Använd befintlig**, och väljer sedan *myResourceGroupLBAZ*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *zon 1* – för den zon där du placerar den virtuella datorn.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen (brandvägg).
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
  
 ![Skapa en virtuell dator](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Skapa en andra virtuell dator med namnet *VM2* i Zon 2 och en tredje virtuell dator i Zon 3, och med *myVnet* som virtuellt nätverk, *myBackendSubnet* som undernät och **myNetworkSecurityGroup* som nätverkssäkerhetsgrupp med steg 1–6.

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVM1** som finns i resursgruppen *myResourceGroupLBAZ*.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Navigera till **Windows Administrationsverktyg**>**Windows PowerShell** på serverdatorn.
5. I PowerShell-fönstret kör du följande kommandon för att installera IIS.servern, ta bort standardfilen iisstart.htm och lägga till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Stäng RDP-sessionen med *myVM1*.
7. Upprepa steg 1 till 6 för att installera IIS och den uppdaterade filen iisstart.htm på *myVM2* och *myVM3*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet ska du konfigurera lastbalanseringsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange lastbalanserare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1*, *VM2* och *VM3*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange *myBackEndPool* som namn på serverdelspoolen.
    - För **Virtuellt nätverk** i den nedrullningsbara menyn klickar du på **myVNet**
    - För **Virtuell dator** klickar du i den nedrullningsbara menyn på **myVM1**.
    - För **IP-adress** klickar du i den nedrullningsbara menyn på IP-adressen för myVM1.
4. Klicka på **Lägg till ny serverdelsresurs** för att lägga till varje virtuella dator (*myVM2* och *myVM3*) för att lägga till i serverdelspoolen för lastbalanseraren.
5. Klicka på **Lägg till**.

    ![Lägga till i backend-adresspoolen (serverdelspoolen)](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Kontrollera att inställningen för lastbalanserarens serverdelspool visar alla tre virtuella datorer – **myVM1**, **myVM2** och **myVM3**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
    - *myHealthProbe* – för hälsoavsökningens namn.
    - **HTTP** – för protokolltypen.
    - *80* – för portnumret.
    - *15* – för antalet **intervall** i sekunder mellan avsökningsförsöken.
    - *2* – för antalet **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig.
4. Klicka på **OK**.

   ![Lägga till en avsökning](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* så att du kan lyssna på port 80 i klientdelen *FrontendLoadBalancer* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Belastningsutjämningsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    - *myHTTPRule* – för belastningsutjämningsregelns namn.
    - **TCP** – för protokolltypen.
    - *80* – för portnumret.
    - *80* – för serverdelsporten.
    - *myBackendPool* – för serverdelspoolens namn.
    - *myHealthProbe* – för hälsoavsökningens namn.
4. Klicka på **OK**.
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se lastbalanseraren fördela trafik på de virtuella datorer som är distribuerade i zonen kan du tvångsuppdatera webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller lastbalanseraren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Standard Load Balancer](load-balancer-standard-overview.md).
