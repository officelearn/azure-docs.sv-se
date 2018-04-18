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
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Belastningsutjämna virtuella datorer över tillgänglighet zoner med Standard belastningsutjämning med hjälp av Azure portal

Den här artikeln steg till att skapa en offentlig belastningen belastningsutjämnaren Standard med en zon redundant klientdel att uppnå uppnå redundans utan beroendet av flera DNS-poster. En frontend IP-adress i en Standard belastningsutjämnare är automatiskt zonredundant. Med hjälp av en zon redundant klientdel för en belastningsutjämnare med en IP-adress kan du nu nå någon virtuell dator i ett virtuellt nätverk i en region som är över alla zoner för tillgänglighet. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter. En eller flera tillgänglighet zoner kan misslyckas med redundans, och datasökvägen kvarstår så länge som en zon i de region är felfri. 

Mer information om hur du använder tillgänglighet zoner med Standard belastningsutjämnaren finns [Standard belastningsutjämnare och tillgänglighet zoner](load-balancer-standard-availability-zones.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Skapa en offentlig Standard belastningsutjämnare

Standard belastningsutjämnaren stöder bara en Standard offentliga IP-adress. När du skapar en ny offentlig IP-adress när du skapar belastningsutjämnaren konfigureras automatiskt som en Standard-SKU-version och är också automatiskt zonredundant.

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs** > **nätverk** > **belastningsutjämnaren**.
2. I den **skapa en belastningsutjämnare** sidan Ange dessa värden för belastningsutjämnaren:
    - *myLoadBalancer* – namnet på belastningsutjämnaren.
    - **Offentliga** – för typ av belastningsutjämnaren.
     - *myPublicIP* - för den nya offentliga IP-adressen som du skapar. Om du vill göra det klickar du på **Välj en offentlig IP-adress**, och klicka sedan på **Skapa nytt**. För namntyp *myPublicIP*, SKU är Standard som standard och välj **redundantzonen** för **tillgänglighet zonen**.
    - *myResourceGroupLBAZ* – namnet på den nya resursgruppen som du skapar.
    - **westeurope** - för platsen.
3. Klicka på **skapa** att skapa belastningsutjämnaren.
   
    ![Skapa en belastningsutjämnare](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Skapa backend-servrar

I det här avsnittet kan du skapa ett virtuellt nätverk, skapa virtuella datorer i olika zoner (zonen 1, zon 2 och zon 3) för den region som ska läggas till i serverdelspoolen belastningsutjämnare och sedan installera IIS på de virtuella datorerna för att testa zonredundant belastningen r ncer. Därför, om en zon misslyckas, hälsoavsökningen för den virtuella datorn i samma zon misslyckas och trafik fortsätter att hanteras av virtuella datorer i andra zoner.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. På den översta vänstra sidan av skärmen klickar du på **skapar du en resurs** > **nätverk** > **för virtuella nätverk** och ange dessa värden för den virtuellt nätverk:
    - *myVnet* – namnet på det virtuella nätverket.
    - *myResourceGroupLBAZ* – namnet på den befintliga resursgruppen
    - *myBackendSubnet* - för undernätsnamnet.
2. Klicka på **skapa** att skapa det virtuella nätverket.

    ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs**, i sökrutan skriver *Nätverkssäkerhetsgruppen*, och på sidan network security group **skapa**.
2. Ange dessa värden på sidan Skapa network security grupp:
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen.
    - *myResourceGroupLBAZ* – namnet på den befintliga resursgruppen.
   
![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Skapa NSG-regler

I det här avsnittet skapar du NSG-regler för att tillåta inkommande anslutningar som använder HTTP och RDP med Azure-portalen.

1. I Azure-portalen klickar du på **alla resurser** i den vänstra menyn och sedan sökningen och klicka på **myNetworkSecurityGroup** som finns i den **myResourceGroupLBAZ** resursgrupp.
2. Under **inställningar**, klickar du på **inkommande säkerhetsregler**, och klicka sedan på **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregel med namnet *myHTTPRule* för en inkommande HTTP-anslutningar som använder port 80:
    - *Tjänsten taggen* – **källa**.
    - *Internet* – **datakällan service-tagg**
    - *80* – **portintervall för mål**
    - *TCP* – **protokoll**
    - *Tillåt* – **åtgärd**
    - *100* för **prioritet**
    - *myHTTPRule* för namn
    - *Tillåt HTTP* – beskrivning
4. Klicka på **OK**.
 
 ![Skapa ett virtuellt nätverk](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Upprepa steg 2 till 4 för att skapa en annan regel med namnet *myRDPRule* för en inkommande RDP-anslutning som använder port 3389 med följande värden:
    - *Tjänsten taggen* – **källa**.
    - *Internet* – **datakällan service-tagg**
    - *3389* – **portintervall för mål**
    - *TCP* – **protokoll**
    - *Tillåt* – **åtgärd**
    - *200* för **prioritet**
    - *myRDPRule* för namn
    - *Tillåt RDP* – beskrivning


### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Klicka på den översta vänstra sidan av skärmen **skapar du en resurs** > **Compute** > **Windows Server 2016 Datacenter** och ange dessa värden för den virtuella datorn:
    - *myVM1* – namnet på den virtuella datorn.        
    - *azureuser* - för administratörsanvändarnamn.    
    - *myResourceGroupLBAZ* – **resursgruppen**väljer **använda befintliga**, och välj sedan *myResourceGroupLBAZ*.
2. Klicka på **OK**.
3. Välj **DS1_V2** för storleken på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningar:
    - *zon 1* – för zonen placerar du den virtuella datorn.
    -  *myVNet* – se till att den är markerad som det virtuella nätverket.
    - *myBackendSubnet* – se till att den är markerad som undernätet.
    - *myNetworkSecurityGroup* – namnet på nätverkssäkerhetsgruppen (brandvägg).
5. Klicka på **inaktiverad** att inaktivera startdiagnostikinställningar.
6. Klicka på **OK**granska inställningarna på sidan Sammanfattning och klicka sedan på **skapa**.
  
 ![Skapa en virtuell dator](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Skapa en andra virtuell dator med namnet *VM2* i zonen 2 och tredje VM i zonen 3 och med *myVnet* som det virtuella nätverket *myBackendSubnet* som undernät, och * *myNetworkSecurityGroup* som en network security grupp med steg 1 – 6.

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på från resurslistan över **myVM1** som finns i den *myResourceGroupLBAZ* resursgruppen.
2. På den **översikt** klickar du på **Anslut** till RDP till den virtuella datorn.
3. Logga in på den virtuella datorn med användarnamnet *azureuser*.
4. Navigera till på serverdatorn **Windows Administrationsverktyg**>**Serverhanteraren**.
5. På sidan Snabbstart i Serverhanteraren klickar du på **Lägg till roller och funktioner**.

   ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. I den **guiden Lägg till roller och funktioner**, använder du följande värden:
    - I den **Välj installationstyp** klickar du på **rollbaserad eller funktionsbaserad installation**.
    - I den **väljer målservern** klickar du på **myVM1**.
    - I den **väljer serverrollen** klickar du på **webbserver (IIS)**.
    - Följ instruktionerna för att slutföra resten av guiden.
2. Stäng RDP-session med den virtuella datorn – *myVM1*.
3. Upprepa steg 1-7 för att installera IIS på virtuella datorer *myVM2* och *myVM3*.

## <a name="create-load-balancer-resources"></a>Skapa load balancer resurser

I det här avsnittet Konfigurera inställningarna för belastningsutjämnaren för en backend-adresspool och en hälsoavsökningen och ange belastningsutjämning och NAT-regler.


### <a name="create-a-backend-address-pool"></a>Skapa en backend-adresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till belastningsutjämnaren. Skapa backend-adresspool *myBackendPool* att inkludera *VM1* och *VM2*.

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** från resurslistan över.
2. Under **inställningar**, klickar du på **serverdelspooler**, klicka på **Lägg till**.
3. På den **Lägg till en serverdelspool** gör följande:
    - Vid namn skriver * myBackEndPool som namn på backend-adresspool.
    - För **för virtuella nätverk**, i listrutan, klickar du på **myVNet**
    - För **virtuella**, i listrutan, klickar på **myVM1**.
    - För **IP-adress**, i listrutan, klickar du på IP-adressen för myVM1.
4. Klicka på **lägga till nya backend-resursen** att lägga till varje virtuell dator (*myVM2* och *myVM3*) att lägga till i serverdelspoolen av belastningsutjämnaren.
5. Klicka på **Lägg till**.

    ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Kontrollera att inställningen belastningen belastningsutjämnaren backend poolen visar alla tre VMs - **myVM1**, **myVM2** och **myVM3**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att belastningsutjämnaren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från belastningsutjämnarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökningen *myHealthProbe* att övervaka hälsotillståndet för de virtuella datorerna.

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** från resurslistan över.
2. Under **inställningar**, klickar du på **hälsoavsökning**, klicka på **Lägg till**.
3. Använd dessa värden för att skapa avsökningen hälsotillstånd:
    - *myHealthProbe* – namnet på hälsoavsökningen.
    - **HTTP** – för protokolltyp.
    - *80* - portnummer.
    - *15* - för antalet **intervall** i sekunder mellan avsökningsförsök.
    - *2* - för antalet **tröskelvärde för ohälsosamt värde** eller avsökningsfel som måste inträffa innan en virtuell dator anses vara felaktig.
4. Klicka på **OK**.

   ![Lägga till en avsökning](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en belastningsutjämningsregel

En belastningsutjämningsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en regel för belastningsutjämnare *myLoadBalancerRuleWeb* för att lyssna på port 80 i klientdelen *FrontendLoadBalancer* och skicka belastningsutjämnad trafik till serverdelsadresspoolen *myBackEndPool* också använder port 80. 

1. Klicka på **alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** från resurslistan över.
2. Under **inställningar**, klickar du på **belastningsutjämningsregler**, klicka på **Lägg till**.
3. Använd dessa värden för att konfigurera regel för belastningsutjämning:
    - *myHTTPRule* – namnet på regel för belastningsutjämning.
    - **TCP** – för protokolltyp.
    - *80* - portnummer.
    - *80* - för backend-port.
    - *myBackendPool* – namnet på serverdelspoolen.
    - *myHealthProbe* – namnet på hälsoavsökningen.
4. Klicka på **OK**.
    
    ![Lägga till en belastningsutjämningsregel](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för belastningsutjämnaren på den **översikt** skärmen. Klicka på **alla resurser** och klicka sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i adressfältet i webbläsaren. IIS-webbservern standardsida visas i webbläsare.

      ![IIS Web server](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs, ta bort resursgruppen, belastningsutjämnare och alla relaterade resurser. Om du vill göra det, väljer du den resursgrupp som innehåller den belastningsutjämnaren och klicka på **ta bort**.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Standard belastningsutjämnaren](load-balancer-standard-overview.md).
