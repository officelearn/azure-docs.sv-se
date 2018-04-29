---
title: Läsa in belastningsutjämning virtuella datorer i en zon - Azure-portalen | Microsoft Docs
description: Skapa en Standard belastningsutjämnare med zonal klientdel att belastningsutjämna virtuella datorer i en zon för tillgänglighet med hjälp av Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/18/2018
ms.author: kumud
ms.openlocfilehash: 41a33436cb0d2c4c2bbfef4888bb704c62e2b91e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="load-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Belastningsutjämna virtuella datorer i en zon för tillgänglighet med Standard belastningsutjämning med hjälp av Azure portal

Den här artikeln går igenom hur du skapar en offentlig [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) adressen med hjälp av Azure portal med en zonal klientdel med hjälp av en offentlig IP-Standard. I det här scenariot kan du ange en viss zon för dina frontend och backend-instanser, justera dina datasökväg och resurser med en viss zon.
Mer information om hur du använder tillgänglighet zoner med Standard belastningsutjämnaren finns [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du vill kan du slutföra den här självstudien med [Azure CLI](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Skapa en offentlig Standard belastningsutjämnare

En standardbelastningsutjämnare stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress när du skapar belastningsutjämnaren konfigureras automatiskt som en Standard-SKU-version och är också automatiskt zonredundant.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Belastningsutjämnare**.
2. Ange dessa värden för belastningsutjämnaren på sidan **Skapa en belastningsutjämnare**:
    - *myLoadBalancer* – namnet på belastningsutjämnaren.
    - **Public** – typen av belastningsutjämnaren.
     - *myPublicIPZonal* - för den nya offentliga IP-adressen som du skapar. Om du vill göra det klickar du på **Välj en offentlig IP-adress**, och klicka sedan på **Skapa nytt**. För namntyp *myPublicIP*, SKU är Standard som standard och väljer **zon 1** för **tillgänglighet zonen**.
    - *myResourceGroupZLB* – namnet på den nya resursgruppen som du skapar.
    - **westeurope** – för platsen.
3. Skapa belastningsutjämnaren genom att klicka på **Skapa**.
   
    ![Skapa zonal Load Balancer Standard med Azure-portalen](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet, skapa ett virtuellt nätverk, skapa två virtuella datorer i samma zon (dvs, 1) att lägga till i serverdelspoolen av din belastningsutjämnare regionen och installera IIS på de virtuella datorerna för att testa zonredundant belastningsutjämnaren. Därför, om en virtuell dator misslyckas, hälsoavsökningen för den virtuella datorn i samma zon misslyckas och trafik fortsätter att hanteras av andra virtuell dator i samma zon.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. På den översta vänstra sidan av skärmen klickar du på **skapar du en resurs** > **nätverk** > **för virtuella nätverk** och ange dessa värden för den virtuellt nätverk:
    - *myVnet* – för det virtuella nätverkets namn.
    - *myResourceGroupZLB* – namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

    ![Skapa ett virtuellt nätverk](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs**, i sökrutan skriver *Nätverkssäkerhetsgruppen*, och på sidan network security group **skapa**.
2. Ange dessa värden på sidan Skapa network security grupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLBAZ* – namnet på den befintliga resursgruppen.
   
    ![Skapa ett virtuellt nätverk](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du NSG-regler för att tillåta inkommande anslutningar som använder HTTP och RDP med Azure-portalen.

1. I Azure-portalen klickar du på **alla resurser** i den vänstra menyn och sedan sökningen och klicka på **myNetworkSecurityGroup** som finns i den **myResourceGroupZLB** resursgrupp.
2. Klicka på **Ingående säkerhetsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för **namn**
    - *Tillåt HTTP* – **beskrivning**
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

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs** > **Compute** > **Windows Server 2016 Datacenter** och ange dessa värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.    
    - *myResourceGroupZLB* – **resursgruppen**väljer **använda befintliga**, och välj sedan *myResourceGroupZLB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *zon 1* – för zonen tillgänglighet placerar du den virtuella datorn.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myVM1PIP* – Standard offentliga IP-adressen som du skapar. Klicka på *Skapa nytt* och sedan för namn, *myVM1PIP*, för zonen väljer **1**. IP-adressen SKU är Standard som standard.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen (brandvägg) som redan finns.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Skapa en andra virtuell dator med namnet *myVM2* i zonen 1 med *myVnet* som det virtuella nätverket *myVM2PIP* som Standard offentliga IP-adress *myBackendSubnet* som undernät, och **myNetworkSecurityGroup* som en network security grupp med steg 1 – 6.

    ![Skapa RDP-regel](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på från resurslistan över **myVM1** som finns i den *myResourceGroupZLB* resursgruppen.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamn och lösenord som du angav när du skapar den virtuella datorn (du kan behöva välja **fler alternativ**, sedan **Använd ett annat konto**, för att ange de autentiseringsuppgifter du angav när du skapa den virtuella datorn), välj sedan **OK**. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
4. Navigera till på serverdatorn **Windows Administrationsverktyg**>**Windows PowerShell**.
6. Lägg till en ny default.htm-fil som visar namnet på den virtuella datorn i PowerShell-fönstret, kör följande kommandon för att installera IIS-servern, ta bort filen default.htm:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Stäng RDP-session med *myVM1*
9. Upprepa steg 1 till 8 för att installera IIS på *myVM2*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för belastningsutjämning

I det här avsnittet ska du konfigurera belastningsutjämningsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange belastningsutjämnare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer* från resurslistan över.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Vid namn skriver *myBackEndPool*, som namn på backend-adresspool.
    - För **för virtuella nätverk**, i listrutan, klickar du på *myVNet*
    - För **virtuella** och **IP-adress** lägga till *myVM1* och *myVM2* och deras motsvarande offentliga IP-adresser.
4. Klicka på **Lägg till**.
5. Kontrollera att inställningen belastningen belastningsutjämnaren backend poolen visar båda de virtuella datorerna - **myVM1** och **myVM2**.
 
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

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. Sidan som innehåller namnet på sidan Web server visas i webbläsare.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Om du vill se belastningsutjämnaren i praktiken kan kraft stoppa den virtuella datorn som visas och uppdatera webbläsaren om du vill se det servernamn som visas i webbläsare.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller belastningsutjämnaren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

- Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
- [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
