---
title: 'Snabbstart: Skapa en offentlig grundläggande lastbalanserare med Azure Portal | Microsoft Docs'
description: Den här snabbstarten visar hur du skapar en offentlig grundläggande lastbalanserare med Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 7451d6ade7f8b042a68f456e604e2919cacab0a5
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378439"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Snabbstart: Skapa en offentlig grundläggande lastbalanserare med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer. Den här snabbstarten visar hur du skapar nätverksresurser, serverdelsservrar och en lastbalanserare på grundläggande prisnivå.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](http://portal.azure.com) för alla uppgifter i den här snabbstarten.

## <a name="create-a-basic-load-balancer"></a>Skapa en grundläggande lastbalanserare

I det här avsnittet skapar du en offentlig grundläggande lastbalansering med hjälp av portalen. Den offentliga IP-adressen konfigureras automatiskt som lastbalanserarens serverdel när du skapar den offentliga IP-adressen och lastbalanserarens resurs genom att använda portalen. Namnet på klientdelen är **myLoadBalancer**.

1. Längst upp till vänster i portalen väljer du **Skapa en resurs** > **Netverk** > **Lastbalanserare**.
2. I fönstret **Skapa lastbalanserare** anger du dessa värden:
   - **myLoadBalancer** för namnet på lastbalanseraren
   - **Public** för typ av lastbalanserare 
   - **myPublicIP** för den offentliga IP-adress som du måste skapa med **SKU**-uppsättningen som **Grundläggande** och **Tilldelning** som **Dynamisk**
   - **myResourceGroupLB** för namnet på den nya resursgruppen
3. Välj **Skapa**.
   
![Skapa en lastbalanserare](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk och du skapar två virtuella datorer för serverdelspoolen för din grundläggande lastbalanserare. Du kan sedan installera IIS (Internet Information Services) på de virtuella datorerna för att testa lastbalanseraren.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Uppe till vänster i portalen väljer du **Nytt** > **Nätverk** > **Virtuellt nätverk**.
2. I **Skapa virtuellt nätverk** anger du dessa värden och väljer sedan **Skapa**:
   - **myVnet** för det virtuella nätverkets namn
   - **myResourceGroupLB** för namnet på den befintliga resursgruppen
   - **myBackendSubnet** för undernätsnamnet

   ![Skapa ett virtuellt nätverk](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Längst upp till vänster i portalen väljer du **Nytt** > **Compute** > **Windows Server 2016 Datacenter**. 
2. Ange dessa värden för den virtuella datorn och välj sedan **OK**:
   - **myVM1** för den virtuella datorns namn.        
   - **azureuser** för administratörens användarnamn.    
   - **myResourceGroupLB** för resursgruppen. (Under **Resursgrupp** väljer du **Använd befintlig** och välj sedan **myResourceGroupLB**.)   
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
   - **myAvailabilitySet** för namnet på den nya tillgänglighetsuppsättning som du skapar.
   - **myVnet** för det virtuella nätverkets namn. (Kontrollera att det är markerat.)
   - **myBackendSubnet** för namnet på undernätet. (Kontrollera att det är markerat.)
   - **myVM1-ip** för den offentliga IP-adress.
   - **myNetworkSecurityGroup** för namnet på den nya nätverkssäkerhetsgrupp (NSG, en typ av brandvägg) som du måste skapa.
5. Inaktivera startdiagnostikinställningar genom att välja **Inaktiverad**.
6. Välj **OK**, granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.
7. Med steg 1 till 6 skapar du en andra virtuell dator med namnet **VM2**, med:
   - **myAvailabilityset** som tillgänglighetsuppsättning.
   - **myVnet** som det virtuella nätverket.
   - **myBackendSubnet** som undernätet.
   - **myNetworkSecurityGroup** som nätverkssäkerhetsgruppen. 

### <a name="create-nsg-rules"></a>Skapa nätverkssäkerhetsgruppsregler

I det här avsnittet skapar du nätverkssäkerhetsgruppsregler som tillåter att inkommande anslutningar använder HTTP och RDP.

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **myNetworkSecurityGroup** i resursgruppen **myResourceGroupLB**.
2. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
3. Ange följande värden för den inkommande säkerhetsregeln med namnet **myHTTPRule** så att den tillåter inkommande HTTP-anslutningar som använder port 80. Välj sedan **OK**.
   - **Tjänstetagg** för **Källa**
   - **Internet** för **Källtjänsttagg**
   - **80** för **målportsintervall**
   - **TCP** för **Protokoll**
   - **Tillåt** för **Åtgärd**
   - **100** för **Prioritet**
   - **myHTTPRule** för **Namn**
   - **Tillåt HTTP** – för **Beskrivning**
 
   ![Skapa en NSG-regel](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Upprepa steg 2 och 3 för att skapa en annan regel med namnet **myRDPRule** för att tillåta en inkommande RDP-anslutning via port 3389. Ange följande värden:
   - **Tjänstetagg** för **Källa**
   - **Internet** för **Källtjänsttagg**
   - **3389** – **målportsintervall**
   - **TCP** för **Protokoll**
   - **Tillåt** för **Åtgärd**
   - **200** för **Prioritet**
   - **myRDPRule** för **Namn**
   - **Tillåt HTTP** – för **Beskrivning**

   

### <a name="install-iis"></a>Installera IIS

1. Välj **Alla resurser** på menyn till vänster. I resurslistan väljer du **myVM1** i resursgruppen **myResourceGroupLB**.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet **azureuser** och lösenordet **Azure123456!**.
4. Gå till **Windows Administrationsverktyg** > **Serverhanteraren** på serverdatorn.
5. I Serverhanteraren väljer du **Hantera** och sedan **Lägg till roller och funktioner**.
   ![Lägger till rollen som serverhanterare](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. Använd följande värden i guiden Lägg till roller och funktioner:
   - Klicka på **Rollbaserad eller funktionsbaserad installation** på sidan **Välj installationstyp**.
   - Välj **myVM1** på sidan **Välj målserver**.
   - Klicka på **Webbserver (IIS)** på sidan **Välj serverroll**.
   - Slutför arbetet genom att följa anvisningarna i guiden. 
7. Upprepa steg 1 till 6 för den virtuella datorn **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Skapa resurser för den grundläggande lastbalanseraren

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning. Du anger regler för lastbalanserare och NAT.


### <a name="create-a-back-end-address-pool"></a>Skapa en backend-adresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen **myBackendPool** så att den omfattar **VM1** och **VM2**.

1. Välj **Alla resurser** i den vänstra menyn och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **Serverdelspooler** och sedan **Lägg till**.
3. På sidan **Lägg till en serverdelspool** gör du följande och väljer sedan **OK**:
   - För **Namn** anger du **myBackEndPool**.
   - Välj **Tillgänglighetsuppsättning** för **Associerad till** i den nedrullningsbara menyn.
   - För **Tillgänglighetsuppsättning** väljer du **myAvailabilitySet**.
   - Välj **Lägg till en målnätverks-IP-konfiguration** och lägg till varje virtuell dator (**myVM1** och **myVM2**) som du skapade till serverdelspoolen.

   ![Lägga till i serverdelsadresspoolen](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Kontrollera att inställningen för serverdelspoolens lastbalanserare visar båda de virtuella datorerna **VM1** och **VM2**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att den grundläggande lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning med namnet **myHealthProbe** så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **Alla resurser** i den vänstra menyn och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.
3. Använd dessa värden och välj sedan **OK**:
   - **myHealthProbe** för hälsoavsökningens namn
   - **HTTP** för protokolltypen
   - **80** för portnumret
   - **15** för **Intervall**, antalet sekunder mellan avsökningsförsök
   - **2** för **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig

   ![Lägga till en avsökning](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

Du använder en lastbalanseringsregel för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar klientdelens IP-konfiguration för inkommande trafik och serverdelens IP-pool för att ta emot trafik, samt även nödvändig käll- och målport. 

Skapa en lastbalanseringsregel med namnet **myLoadBalancerRuleWeb** för att avlyssna port 80 i klientdelen **LoadBalancerFrontEnd**. Regeln används även för att skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen **myBackEndPool**, också det med port 80. 

1. Välj **Alla resurser** i den vänstra menyn och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar** väljer du **Belastningsutjämningsregler** och väljer sedan **Lägg till**.
3. Använd dessa värden och välj sedan **OK**:
   - **myHTTPRule** för lastbalanseringsregelns namn
   - **TCP** för protokolltypen
   - **80** för portnumret
   - **80** för serverdelsport
   - **myBackendPool** för serverdelspoolens namn
   - **myHealthProbe** för hälsoavsökningens namn
    
   ![Lägga till en lastbalanseringsregel](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **Alla resurser** och sedan **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när du inte längre behöver dem. Markera den resursgrupp som innehåller lastbalanseraren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en resursgrupp, nätverksresurser och serverdelsservrar. Du använde sedan de här resurserna för att skapa en grundläggande lastbalanserare. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
