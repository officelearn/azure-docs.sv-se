---
title: 'Snabbstart: Skapa en standardbelastningsutjämnare – Azure Portal | Microsoft Docs'
description: I den här snabbstarten visas hur du skapar en offentlig standardbelastningsutjämnare med Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/18
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: dc7d8a4cc5b755504c2430be187d3ab2b46c51d1
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabbstart: Skapa en standardbelastningsutjämnare som belastningsutjämnar virtuella datorer med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en belastningsutjämnare som belastningsutjämnar virtuella datorer (VM). Den här snabbstartsguiden visar hur du belastningsutjämnar virtuella datorer med en standardbelastningsutjämnare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-load-balancer"></a>Skapa en offentlig belastningsutjämnare

I det här avsnittet skapar du en offentlig belastningsutjämnare som hjälper till att belastningsutjämna virtuella datorer. En standardbelastningsutjämnare stöder endast offentliga IP-standardadresser. När du skapar en standardbelastningsutjämnare, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för standardbelastningsutjämnaren. 

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Belastningsutjämnare**.
2. Ange dessa värden för belastningsutjämnaren på sidan **Skapa en belastningsutjämnare**:
    - *myLoadBalancer* – namnet på belastningsutjämnaren.
    - **Public** – typen av belastningsutjämnaren.
     - *myPublicIP* – för den **nya** offentliga IP-adress som du skapar.
    - *myResourceGroupSLB* – namnet på den **nya** resursgrupp som du väljer för att skapa.
    - **westeurope** – för platsen.
3. Skapa belastningsutjämnaren genom att klicka på **Skapa**.
   
    ![Skapa en belastningsutjämnare](./media/load-balancer-standard-public-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du först ett virtuellt nätverk, sedan två virtuella datorer för din belastningsutjämnares serverdelspool och installerar sedan IIS på de virtuella datorerna, vilket gör det lättare att testa belastningsutjämnaren.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Klicka på **Nytt** > **Nätverk** > **Virtuella nätverk** överst till vänster på skärmen och ange följande värden för det virtuella nätverket:
    - *myVnet* – det virtuella nätverkets namn.
    - *myResourceGroupSLB* – namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

    ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Klicka på **Nytt** > **Beräkna** > **Windows Server 2016 Datacenter** överst till vänster på skärmen och ange följande värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – administratörens användarnamn.    
    - *myResourceGroupSLB* – för **Resursgrupp**väljer du **Använd befintlig**, och väljer sedan *myResourceGroupSLB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *myAvailabilitySet* – för namnet på den nya tillgänglighetsuppsättning som du skapar.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
    - *myNetworkSecurityGroup* – namnet på den nya nätverkssäkerhetsgrupp (brandvägg) som du måste skapa.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Skapa, med hjälp av steg 1-6, en andra virtuell dator med namnet *VM2* med *myAvailabilityset* som tillgänglighetsuppsättning, *myVnet* som dess virtuella nätverk, *myBackendSubnet* som dess undernät och **myNetworkSecurityGroup* som dess nätverkssäkerhetsgrupp. 

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar använder HTTP och RDP.

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myNetworkSecurityGroup** som finns i resursgruppen **myResourceGroupSLB**.
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
 
 ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-portal/8-load-balancer-nsg-rules.png)
5. Skapa, genom att upprepa steg 2 till 4, en annan regel med namnet *myRDPRule* som tillåter att en inkommande RDP-anslutning använder port 3389 med följande värden:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *3389* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *200* för **Prioritet**
    - *myRDPRule* för namn
    - *Tillåt HTTP* – för beskrivning

### <a name="install-iis"></a>Installera IIS

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVM1** som finns i resursgruppen *myResourceGroupLB*.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
5. Klicka på **Lägg till roller och funktioner** i Serverhanteraren.
6. Använd följande värden i guiden **Lägg till roller och funktioner**:
    - Klicka på **Rollbaserad eller funktionsbaserad installation** på sidan **Välj installationstyp**.
    - Klicka på **myVM1** på sidan **Välj målserver**
    - Klicka på **Webbserver (IIS)** på sidan **Välj serverroll**
    - Slutför arbetet genom att följa anvisningarna i guiden 
7. Upprepa steg 1 till 6 för den virtuella datorn *myVM2*.

## <a name="create-load-balancer-resources"></a>Skapa resurser för belastningsutjämning

I det här avsnittet ska du konfigurera belastningsutjämningsinställningarna för en serverdelsadresspool och en hälsoavsökning, och ange belastningsutjämnare och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange * myBackEndPool som namn på serverdelspoolen.
    - Klicka på **Tillgänglighetsuppsättning** för **Associerad till** i den nedrullningsbara menyn
    - Klicka på **myAvailabilitySet** för **Tillgänglighetsuppsättning**.
    - Klicka på **Lägg till en målnätverks-IP-konfiguration** och lägg till varje virtuell dator (*myVM1* & *myVM2*) som du skapade till serverdelspoolen.
    - Klicka på **OK**.

    ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/load-balancer-standard-public-portal/3-load-balancer-backend-02.png)

3. Kontrollera att inställningen för serverdelspoolens belastningsutjämnare visar båda de virtuella datorerna **VM1** och **VM2**.

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

   ![Lägga till en avsökning](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

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
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för belastningsutjämnaren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller belastningsutjämnaren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Standardbelastningsutjämnare](load-balancer-standard-overview.md).
