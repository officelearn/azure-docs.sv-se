---
title: 'Snabbstart: Skapa en Standard Load Balancer – Azure-portalen'
titlesuffix: Azure Load Balancer
description: I den här snabbstarten visas hur du skapar en offentlig Standard Load Balancer med Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 97d1cf2817ebfbf2eb1a6ba5a4d20d457b6369c6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961745"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabbstart: Skapa en Standard Load Balancer som lastbalanserar virtuella datorer med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer (VM). Den här snabbstartsguiden visar hur du lastbalanserar virtuella datorer med en Standard Load Balancer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-load-balancer"></a>Skapa en offentlig lastbalanserare

I det här avsnittet skapar du en offentlig lastbalanserare som hjälper till att lastbalansera virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. 

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Lastbalanserare**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *MyResourceGroupSLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Västeuropa**.                                        |
    | Typ          | Välj **Offentligt**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |
3. På fliken **Granska + skapa** klickar du på **Skapa**.   

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du först ett virtuellt nätverk, sedan två virtuella datorer för din lastbalanserares serverdelspool och installerar sedan IIS på de virtuella datorerna, vilket gör det lättare att testa lastbalanseraren.

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
    - *myResourceGroupSLB* – för **Resursgrupp**väljer du **Använd befintlig**, och väljer sedan *myResourceGroupSLB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    1. Kontrollera att *myVNet* är valt som det virtuella nätverket och att *myBackendSubnet* är valt som undernätet.
    2. Välj **Standard** för **Offentlig IP-adress** i rutan **Skapa offentlig IP-adress** och välj sedan **OK**.
    3. Välj **Avancerat** för **Nätverkssäkerhetsgrupp** och gör sedan följande:
        1. Välj *Nätverkssäkerhetsgrupp (brandvägg) och välj **Skapa ny** på sidan **Välj nätverkssäkerhetsgrupp**. 
        2. Ange *myNetworkSecurityGroup* för **Namn** på sidan **Skapa nätverkssäkerhetsgrupp** och välj **OK**.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Med hjälp av steg 1-6 skapar du en andra virtuell dator med namnet *VM2* med *myVnet* som dess virtuella nätverk, *myBackendSubnet* som dess undernät och **myNetworkSecurityGroup* som dess nätverkssäkerhetsgrupp. 

### <a name="create-nsg-rule"></a>Skapa NSG-regel

I det här avsnittet skapar du en NSG-regel (nätverkssäkerhetsgrupp) som tillåter inkommande anslutningar via HTTP.

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

## <a name="create-load-balancer-resources"></a>Skapa resurser för lastbalansering

I det här avsnittet konfigurerar du inställningarna för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning, och anger en regel för lastbalanseraren.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
   - Ange *myBackendPool* som namn på serverdelspoolen.
   - Välj *myVNet* för **Virtuellt nätverk**.
   - Lägg till *myVM1* och *my VM2* under **Virtuell dator** tillsammans med deras respektive IP-adresser och välj sedan **Lägg till**.
    - Klicka på **OK**.

3. Kontrollera att inställningen för serverdelspoolens lastbalanserare visar båda de virtuella datorerna **VM1** och **VM2**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
    - *myHealthProbe* – för hälsoavsökningens namn.
    - **HTTP** – för protokolltypen.
    - *80* – för portnumret.
    - *Healthprobe.aspx* – för URI-sökvägen. Du kan antingen ersätta värdet med något annat URI eller behålla standardvärdet **”\\”** för sökvägen för att hämta standard-URI:n.
    - *15* – för antalet **intervall** i sekunder mellan avsökningsförsöken.
    - *2* – för antalet **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig.
4. Klicka på **OK**.

   ![Lägga till en avsökning](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

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
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Klicka på **Alla resurser** och sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller lastbalanseraren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Standard Load Balancer, anslutit virtuella datorer till den, konfigurerat trafikregeln för lastbalansering, hälsoavsökningen och sedan testat lastbalanseraren. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
