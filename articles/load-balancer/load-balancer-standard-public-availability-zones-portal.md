---
title: Läsa in belastningsutjämning virtuella datorer över tillgänglighet zoner - Azure-portalen | Microsoft Docs
description: Skapa en Standard belastningsutjämnare med zonredundant klientdel att belastningsutjämna virtuella datorer över tillgänglighet zoner med hjälp av Azure portal
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
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Belastningsutjämna virtuella datorer över tillgänglighet zoner med Standard belastningsutjämning med hjälp av Azure portal

Den här artikeln steg till att skapa en offentlig belastningen belastningsutjämnaren Standard med en zon redundant klientdel att uppnå uppnå redundans utan beroendet av flera DNS-poster. En frontend IP-adress i en Standard belastningsutjämnare är automatiskt zonredundant. Med hjälp av en zon redundant klientdel för en belastningsutjämnare med en IP-adress kan du nu nå någon virtuell dator i ett virtuellt nätverk i en region som är över alla zoner för tillgänglighet. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. En eller flera tillgänglighet zoner kan misslyckas med redundans, och datasökvägen kvarstår så länge som en zon i de region är felfri. 

Mer information om hur du använder tillgänglighet zoner med Standard belastningsutjämnaren finns [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Skapa en offentlig Standard belastningsutjämnare

En standardbelastningsutjämnare stöder endast offentliga IP-standardadresser. När du skapar en ny offentlig IP-adress när du skapar belastningsutjämnaren konfigureras automatiskt som en Standard-SKU-version och är också automatiskt zonredundant.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Belastningsutjämnare**.
2. Ange dessa värden för belastningsutjämnaren på sidan **Skapa en belastningsutjämnare**:
    - *myLoadBalancer* – namnet på belastningsutjämnaren.
    - **Public** – typen av belastningsutjämnaren.
     - *myPublicIP* - för den nya offentliga IP-adressen som du skapar. Om du vill göra det klickar du på **Välj en offentlig IP-adress**, och klicka sedan på **Skapa nytt**. För namntyp *myPublicIP*, SKU är Standard som standard och välj **redundantzonen** för **tillgänglighet zonen**.
    - *myResourceGroupLBAZ* – namnet på den nya resursgruppen som du skapar.
    - **westeurope** – för platsen.
3. Skapa belastningsutjämnaren genom att klicka på **Skapa**.
   
    ![Skapa en belastningsutjämnare](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet kan du skapa ett virtuellt nätverk, skapa virtuella datorer i olika zoner (zonen 1, zon 2 och zon 3) för den region som ska läggas till i serverdelspoolen belastningsutjämnare och sedan installera IIS på de virtuella datorerna för att testa zonredundant belastningen r ncer. Därför, om en zon misslyckas, hälsoavsökningen för den virtuella datorn i samma zon misslyckas och trafik fortsätter att hanteras av virtuella datorer i andra zoner.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. På den översta vänstra sidan av skärmen klickar du på **skapar du en resurs** > **nätverk** > **för virtuella nätverk** och ange dessa värden för den virtuellt nätverk:
    - *myVnet* – för det virtuella nätverkets namn.
    - *myResourceGroupLBAZ* – namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

    ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs**, i sökrutan skriver *Nätverkssäkerhetsgruppen*, och på sidan network security group **skapa**.
2. Ange dessa värden på sidan Skapa network security grupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLBAZ* – namnet på den befintliga resursgruppen.
   
![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du NSG-regler för att tillåta inkommande anslutningar som använder HTTP och RDP med Azure-portalen.

1. I Azure-portalen klickar du på **alla resurser** i den vänstra menyn och sedan sökningen och klicka på **myNetworkSecurityGroup** som finns i den **myResourceGroupLBAZ** resursgrupp.
2. Klicka på **Ingående säkerhetsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för namn
    - *Tillåt HTTP* – för beskrivning
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

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs** > **Compute** > **Windows Server 2016 Datacenter** och ange dessa värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.    
    - *myResourceGroupLBAZ* – **resursgruppen**väljer **använda befintliga**, och välj sedan *myResourceGroupLBAZ*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *zon 1* – för zonen placerar du den virtuella datorn.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen (brandvägg).
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
  
 ![Skapa en virtuell dator](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Skapa en andra virtuell dator med namnet *VM2* i zonen 2 och tredje VM i zonen 3 och med *myVnet* som det virtuella nätverket *myBackendSubnet* som undernät, och * *myNetworkSecurityGroup* som en network security grupp med steg 1 – 6.

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på från resurslistan över **myVM1** som finns i den *myResourceGroupLBAZ* resursgruppen.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
5. På sidan Snabbstart i Serverhanteraren klickar du på **Lägg till roller och funktioner**.

   ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. Använd följande värden i guiden **Lägg till roller och funktioner**:
    - Klicka på **Rollbaserad eller funktionsbaserad installation** på sidan **Välj installationstyp**.
    - I den **väljer målservern** klickar du på **myVM1**.
    - I den **väljer serverrollen** klickar du på **webbserver (IIS)**.
    - Följ instruktionerna för att slutföra resten av guiden.
2. Stäng RDP-session med den virtuella datorn – *myVM1*.
3. Upprepa steg 1-7 för att installera IIS på virtuella datorer *myVM2* och *myVM3*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för belastningsutjämning

I det här avsnittet ska du konfigurera belastningsutjämningsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange belastningsutjämnare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange * myBackEndPool som namn på serverdelspoolen.
    - För **för virtuella nätverk**, i listrutan, klickar du på **myVNet**
    - För **virtuella**, i listrutan, klickar på **myVM1**.
    - För **IP-adress**, i listrutan, klickar du på IP-adressen för myVM1.
4. Klicka på **lägga till nya backend-resursen** att lägga till varje virtuell dator (*myVM2* och *myVM3*) att lägga till i serverdelspoolen av belastningsutjämnaren.
5. Klicka på **Lägg till**.

    ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Kontrollera att inställningen belastningen belastningsutjämnaren backend poolen visar alla tre VMs - **myVM1**, **myVM2** och **myVM3**.

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
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för belastningsutjämnaren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller belastningsutjämnaren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
