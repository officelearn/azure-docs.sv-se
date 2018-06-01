---
title: 'Självstudiekurs: Virtuella datorer med belastningsutjämnare i en zon – Azure Portal | Microsoft Docs'
description: Den här kursen visar hur du skapar en standardbelastningsutjämnare med zonindelad klientdel för att belastningsutjämna virtuella datorer i en tillgänglighetszon med hjälp av Azure Portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 52d0aeabab173caf4460827ca0d5984070688f0e
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304733"
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Självstudiekurs: Belastningsutjämna virtuella datorer i en tillgänglighetszon med standardbelastningsutjämnare med hjälp av Azure Portal

Den här kursen går igenom hur du skapar en offentlig [belastningsutjämningsstandard](https://aka.ms/azureloadbalancerstandard) med en zonindelad klientdel med hjälp av en offentlig IP-standardadress med Azure Portal. I det här scenariot anger du en viss zon för dina klientdels- och serverdelsinstanser för att justera din datasökväg och dina resurser med en viss zon. Lär dig att:

> [!div class="checklist"]
> * skapa en Azure-standardbelastningsutjämnare med zonindelad klientdel
> * skapa nätverkssäkerhetsgrupper för att definiera regler för inkommande trafik
> * skapa zonindelade virtuella datorer och ansluta till en belastningsutjämnare
> * skapa hälsoavsökning för belastningsutjämnaren
> * skapa trafikregler för belastningsutjämnaren
> * skapa en grundläggande IIS-webbplats
> * visa en belastningsutjämnare i praktiken

Mer information om hur du använder tillgänglighetszoner med standardbelastningsutjämnare finns i [Standardbelastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du vill kan du slutföra den här självstudien med [Azure CLI](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Skapa en offentlig Standard Load Balancer

En standardbelastningsutjämnare stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress medan du skapar belastningsutjämnaren konfigureras den automatiskt som en standard-SKU-version och blir också automatiskt zonredundant.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Belastningsutjämnare**.
2. Ange dessa värden för belastningsutjämnaren på sidan **Skapa en belastningsutjämnare**:
    - *myLoadBalancer* – namnet på belastningsutjämnaren.
    - **Public** – typen av belastningsutjämnaren.
     - *myPublicIPZonal* – för den nya offentliga IP-adress som du skapar. Om du vill göra det klickar du på **Välj en offentlig IP-adress** och klickar sedan på **Skapa ny**. För namntypen *myPublicIP* är SKU är standard och väljer **Zon 1** som **tillgänglighetszone**.
    - *myResourceGroupZLB* – för namnet på den nya resursgrupp som du skapar.
    - **westeurope** – för platsen.
3. Skapa belastningsutjämnaren genom att klicka på **Skapa**.
   
    ![Skapa zonindelad belastningsutjämningsstandard med Azure Portal](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, skapar två virtuella datorer i samma zon (zon 1) för regionen för att lägga till i serverdelspoolen för belastningsutjämnaren och installerar sedan IIS på de virtuella datorerna för att testa den zonredundanta belastningsutjämnaren. Om en virtuell dator misslyckas så misslyckas därför hälsoavsökningen för virtuell dator i samma zon, och trafiken fortsätter att fungera med en annan virtuell dator i samma zon.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Klicka på **Skapa en resurs** > **Nätverk** > **Virtuella nätverk** överst till vänster på skärmen och ange följande värden för det virtuella nätverket:
    - *myVnet* – för det virtuella nätverkets namn.
    - *myResourceGroupZLB* – för namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

    ![Skapa ett virtuellt nätverk](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Högst upp till vänster på skärmen klickar du på **Skapa en resurs**, i sökrutan skriver du *Nätverkssäkerhetsgrupp*, och på nätverkssäkerhetsgruppens sidan klickar du på **Skapa**.
2. Ange dessa värden på sidan Skapa nätverkssäkerhetsgrupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLB* – för namnet på den befintliga resursgruppen.
   
    ![Skapa ett virtuellt nätverk](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar använder HTTP och RDP med Azure Portal.

1. I Azure Portal klickar du på **Alla resurser** i den vänstra menyn. Sök sedan och klicka på **myNetworkSecurityGroup** som finns i resursgruppen **myResourceGroupZLB**.
2. Klicka på **Ingående säkerhetsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för **Namn**
    - *Tillåt HTTP* – för **Beskrivning**
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

    ![Skapa RDP-regel](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Klicka på **Skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter** överst till vänster på skärmen och ange följande värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.    
    - *myResourceGroupZLB* – för **Resursgrupp**väljer du **Använd befintlig**, och väljer sedan *myResourceGroupZLB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *zon 1* – för den tillgänglighetszon där du placerar den virtuella datorn.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myVM1PIP* – för den offentliga standard-IP-adress du skapar. Klicka på *Skapa ny* ange sedan *myVM1PIP* som namntyp. Välj **1** för Zon. IP-adressens SKU är Standard som standard.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – för namnet på den nätverkssäkerhetsgrupp (brandvägg) som redan finns.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Skapa en andra virtuell dator med namnet *myVM2* i Zon 1 med *myVnet* som det virtuella nätverket, *myVM2PIP* som offentlig standard-IP-adress, *myBackendSubnet* som undernät, och **myNetworkSecurityGroup* som nätverkssäkerhetsgrupp med steg 1–6.

    ![Skapa RDP-regel](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVM1** som finns i resursgruppen *myResourceGroupZLB*.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
4. Navigera till **Windows Administrationsverktyg**>**Windows PowerShell** på serverdatorn.
6. I PowerShell-fönstret kör du följande kommandon för att installera IIS.servern, ta bort standardfilen iisstart.htm och lägga till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Stäng RDP-session med *myVM1*
8. Upprepa steg 1 till 7 för att installera IIS på *myVM2*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för belastningsutjämning

I det här avsnittet ska du konfigurera belastningsutjämningsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange belastningsutjämnare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer* i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange *myBackEndPool* som namn på serverdelspoolen.
    - För **Virtuellt nätverk** i den nedrullningsbara menyn klickar du på *myVNet*
    - För **Virtuell dator** och **IP-adress** lägger du till *myVM1* och *myVM2* samt motsvarande offentliga IP-adresser.
4. Klicka på **Lägg till**.
5. Kontrollera att inställningen för serverdelspoolens belastningsutjämnare visar båda de virtuella datorerna – **myVM1** och **myVM2**.
 
    ![Skapa serverdelspool](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att belastningsutjämnaren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från belastningsutjämnarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

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

### <a name="create-a-load-balancer-rule"></a>Skapa en belastningsutjämningsregel

En belastningsutjämningsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa belastningsutjämningsregeln *myLoadBalancerRuleWeb* så att du kan lyssna på port 80 i klientdelen *FrontendLoadBalancer* och skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

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
    
    ![Lägga till en belastningsutjämningsregel](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för belastningsutjämnaren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**. 

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Sidan som innehåller namnet på webbserversidan visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Om du vill se belastningsutjämnaren i aktion kan du tvångsstoppa den virtuella datorn som visas och uppdatera webbläsaren för att se det andra servernamnet i webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller belastningsutjämnaren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
