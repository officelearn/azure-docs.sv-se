---
title: 'Snabbstart: Skapa en Standard Load Balancer – Azure-portalen'
titlesuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en Standardbelastningsutjämnare med hjälp av Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 77e322e32d19433d9ce4629c2e04c8bbd7e17f3f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858243"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabbstart: Skapa en Standard Load Balancer som lastbalanserar virtuella datorer med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer (VM). Den här snabbstartsguiden visar hur du lastbalanserar virtuella datorer med en Standard Load Balancer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

I det här avsnittet skapar du en Standardbelastningsutjämnare som hjälper till att belastningsutjämna virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. 

1. Längst upp till vänster på skärmen Välj **skapa en resurs** > **nätverk** > **belastningsutjämnaren**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa nytt** och skriv *myResourceGroupSLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Västeuropa**.                                        |
    | Type          | Välj **Offentligt**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |
3. I den **granska + skapa** fliken **skapa**.   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa belastningsutjämnaren resurser

I det här avsnittet ska du konfigurera inställningar för belastningsutjämnaren för serverdelsadresspoolen en hälsoavsökning och anger en regel för belastningsutjämnaren.

### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna, en backend-adresspool som innehåller IP-adresser för virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* att inkludera virtuella datorer för belastningsutjämning Internettrafik.

1. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och välj sedan **myLoadBalancer** i resurslistan.
2. Under **inställningar**väljer **serverdelspooler**och välj sedan **Lägg till**.
3. På den **Lägg till en serverdelspool** för namn, typ *myBackendPool*, som namn för serverdelspoolen och välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

För att belastningsutjämnaren ska övervaka status för din app måste använda du en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer från belastningsutjämnaren rotationen baserat på deras svar på hälsokontroller dynamiskt. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och välj sedan **myLoadBalancer** i resurslistan.
2. Under **inställningar**väljer **hälsoavsökningar**och välj sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHealthProbe*. |
    | Protokoll | Välj **HTTP**. |
    | Port | Ange *80*.|
    | Intervall | Ange *15* för antal **intervall** i sekunder mellan avsökningsförsöken. |
    | Tröskelvärde för Ej felfri | Välj *2* för antal **tröskelvärde för ej felfri** eller avsökningsfel som måste inträffa innan en virtuell dator betraktas som defekt.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa belastningsutjämningsregeln *myLoadBalancerRuleWeb* för att lyssna på port 80 i klientdelen *FrontendLoadBalancer* och skicka belastningsutjämnad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* också använder port 80. 

1. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och välj sedan **myLoadBalancer** i resurslistan.
2. Under **inställningar**väljer **belastningsutjämningsregler**och välj sedan **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHTTPRule*. |
    | Protokoll | Välj **TCP**. |
    | Port | Ange *80*.|
    | Serverdelsport | Ange *80*. |
    | Serverdelspool | Välj *myBackendPool*.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
4. Lämna resten av standardinställningarna och välj **OK**.
4. Välj **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet, skapa ett virtuellt nätverk, skapar två virtuella datorer för serverdelspoolen för belastningsutjämnaren och installerar sedan IIS på de virtuella datorerna för att testa belastningsutjämnaren.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myVNet*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj befintlig resurs - *myResourceGroupSLB*. |
    | Plats | Välj **Västeuropa**.|
    | Undernät – Namn | Ange *myBackendSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
1. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Standard Load Balancer har endast stöd för virtuella datorer med Standard IP-adresser i serverdelspoolen. I det här avsnittet skapar du två virtuella datorer (*myVM1* och *myVM2*) med en Standard offentlig IP-adress i två olika zoner (*zon 1* och *zon 2*) som läggs till i serverdelspoolen för Standard Load Balancer som du skapade tidigare.

1. Uppe till vänster i portalen väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 Datacenter**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumeration** > **Resursgrupp**: Välj **myResourceGroupSLB**.
   - **Instansinformation** > **Namn på virtuell dator**: Typ *myVM1*.
   - **Information om-instansen** > **Region** > Välj **Västeuropa**.
   - **Information om-instansen** > **tillgänglighetsalternativ** > Välj **tillgänglighetszoner**. 
   - **Information om-instansen** > **tillgänglighetszon** > Välj **1**.
  
1. Välj fliken **Nätverk** eller välj **Nästa: Diskar** och sedan **Nästa: Nätverk**. 
   
   - Kontrollera att följande har valts:
       - **Virtuellt nätverk**: *myVnet*
       - **Undernät**: *myBackendSubnet*
       - **Offentlig IP-adress** > Välj **Skapa nytt**, och i den **skapa offentlig IP-adress** fönstret för **SKU**väljer **Standard**, och för **tillgänglighetszon**väljer **redundantzonen**, och välj sedan **OK**.
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Typ *myNetworkSecurityGroup*, och välj **OK**.
   - Utför följande steg för att göra den virtuella datorn tillhör Belastningsutjämnarens serverdelspool har:
        - I **Utjämning av nätverksbelastning**, för **placera den virtuella datorn bakom en befintlig belastningsutjämningslösning?** väljer **Ja**.
        - I **inställningar för belastningsutjämning**, för **Belastningsutjämningsalternativ**väljer **Azure-belastningsutjämnare**.
        - För **markerar du en belastningsutjämnare**, *myLoadBalancer*. 
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**. 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Följ stegen för att skapa en andra virtuell dator med namnet *myVM2*, med en Standard-SKU offentlig IP-adress med namnet *myVM2 ip*, och **tillgänglighetszon** inställningen för den **2** och de andra inställningarna identiskt *myVM1*. 

### <a name="create-nsg-rule"></a>Skapa NSG-regel

I det här avsnittet skapar du en regel för nätverkssäkerhetsgrupp som tillåter inkommande anslutningar via HTTP.

1. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och sedan från resurserna väljer **myNetworkSecurityGroup** som finns i **myResourceGroupSLB** resursgrupp.
2. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - *Tjänstetagg* – för **Källa**.
    - *Internet* – för **Källtjänsttagg**
    - *80* – för **målportsintervall**
    - *TCP* – för **Protokoll**
    - *Tillåt* – för **Åtgärd**
    - *100* för **Prioritet**
    - *myHTTPRule* för namn
    - *Tillåt HTTP* – för beskrivning
4. Välj **OK**.
 
### <a name="install-iis"></a>Installera IIS

1. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och välj sedan i resurslistan **myVM1** som finns i den  *myResourceGroupSLB* resursgrupp.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
5. I Serverhanteraren väljer **Lägg till roller och funktioner**.
6. Använd följande värden i guiden **Lägg till roller och funktioner**:
    - I den **Välj installationstyp** väljer **rollbaserad eller funktionsbaserad installation**.
    - I den **väljer målservern** väljer **myVM1**
    - I den **Välj serverroll** väljer **webbserver (IIS)**
    - Slutför arbetet genom att följa anvisningarna i guiden 
7. Upprepa steg 1 till 6 för den virtuella datorn *myVM2*.

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **alla tjänster** i den vänstra menyn och väljer **alla resurser**, och välj sedan **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

Om du vill se hur belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer som kör din app kan du framtvinga uppdatering av webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, belastningsutjämnaren och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen (*myResourceGroupSLB*) som innehåller belastningsutjämnaren och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten du skapade en Standardbelastningsutjämnare, anslutna virtuella datorer, konfigurerade trafikregel belastningsutjämnaren, hälsoavsökning och testade därefter belastningsutjämnaren. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
