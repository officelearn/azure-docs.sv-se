---
title: 'Självstudie: belastningsutjämna Internet trafik till virtuella datorer – Azure Portal'
titleSuffix: Azure Load Balancer
description: I den här självstudien visas hur du skapar och hanterar en offentlig Standard Load Balancer med Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4d4703ccb4ee96eb69a780f91eae1eb6da9e1578
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225185"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Självstudie: Belastningsutjämna internettrafik till virtuella datorer med hjälp av Azure-portalen

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. I den här självstudien får du lära dig mer om de olika komponenterna i den Azure Standard Load Balancer som distribuerar internettrafik till virtuella datorer och ger hög tillgänglighet. Lär dig att:


> [!div class="checklist"]
> * Skapa en Azure Load Balancer
> * Skapa Load Balancer resurser
> * Skapa virtuella datorer och installera IIS-servern
> * Visa Load Balancer i praktiken
> * Lägga till och ta bort virtuella datorer från en Load Balancer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

I det här avsnittet skapar du en Standard Load Balancer som hjälper till att belastningsutjämna virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. 

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Lastbalanserare**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *myResourceGroupSLB* i text rutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Västeuropa**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |

3. På fliken **Granska + skapa** klickar du på **Skapa**.

   ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa Load Balancer resurser

I det här avsnittet konfigurerar du Load Balancer inställningar för en backend-adresspool, en hälso avsökning och anger en balans regel.

### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna innehåller en backend-adresspool IP-adresserna för de virtuella nätverkskorten som är anslutna till Load Balancer. Skapa *myBackendPool* för backend-adresspoolen för att inkludera virtuella datorer för belastnings utjämning av Internet trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. På sidan **Lägg till en server dels grupp** anger du *myBackendPool*som namn på din backend-pool. Välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill tillåta att Load Balancer övervakar appens status använder du en hälso avsökning. Hälso avsökningen lägger till eller tar bort virtuella datorer dynamiskt från Load Balancer rotation baserat på deras svar på hälso kontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
     
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHealthProbe*. |
    | Protokoll | Välj **http**. |
    | Port | Ange *80*.|
    | Intervall | Ange *15* som **intervall** i sekunder mellan avsöknings försök. |
    | Tröskelvärde för ej felfri | Välj *2* för antalet fel i **tröskeln** eller på varandra följande avsöknings fel som måste inträffa innan en virtuell dator betraktas som ohälsosam.|
    
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en Load Balancer regel *myLoadBalancerRuleWeb* för att lyssna på port 80 i klient delens *FrontendLoadBalancer* och skicka belastningsutjämnad nätverks trafik till backend-adresspoolen *myBackEndPool* även med port 80.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myLoadBalancer** i listan resurser.
2. Klicka på **Belastningsutjämningsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd de här värdena för att konfigurera belastnings Utjämnings regeln:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHTTPRule*. |
    | Protokoll | Välj **TCP**. |
    | Port | Ange *80*.|
    | Backend-port | Ange *80*. |
    | Backend-pool | Välj *myBackendPool*.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
    
4. Lämna resten av standardinställningarna och välj **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, skapar tre virtuella datorer för den Load Balancerens backend-pool och installerar sedan IIS på de virtuella datorerna för att testa Load Balancer.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
2. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myVNet*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj befintlig resurs- *myResourceGroupSLB*. |
    | Plats | Välj **Västeuropa**.|
    | Undernät – namn | Ange *myBackendSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
    
3. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Standard Load Balancer stöder bara virtuella datorer med standard-IP-adresser i backend-poolen. I det här avsnittet ska du skapa tre virtuella datorer (*myVM1*, *myVM2*och *MyVM3*) med en offentlig standard-IP-adress i tre olika zoner (*zon 1*, *zon 2*och *zon 3*) som läggs till i backend-poolen för standard Load Balancer som skapades tidigare.

1. Uppe till vänster i portalen väljer du **Skapa en resurs** > **Beräkning** > **Windows Server 2016 Datacenter**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumerations** > **resurs grupp**: Välj **myResourceGroupSLB**.
   - **Instans information** > **namn på virtuell dator**: Skriv *myVM1*.
   - **Instans information** > **region** > Välj **Europa, västra**.
   - **Instans information** > **tillgänglighets alternativ** > Välj **tillgänglighets zoner**. 
   - **Instans information** > **tillgänglighets zon** > Välj **1**.
  
1. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**. 
   
   - Kontrollera att följande har valts:
       - **Virtuellt nätverk**: **myVnet**
       - **Undernät**: **myBackendSubnet**
       - **Offentlig ip** > Välj **Skapa ny**och i fönstret **skapa offentlig IP-adress** för **SKU**väljer du **standard**och för **tillgänglighets zon**väljer du **zon-redundant**
      
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Skriv *myNetworkSecurityGroup*och välj **OK**.

   - Utför följande steg för att göra den virtuella datorn till en del av Load Balancers backend-pool:
        - I **belastnings utjämning**väljer du **Ja**för att **Placera den virtuella datorn bakom en befintlig belastnings Utjämnings lösning?** .
        - I inställningar **för belastnings utjämning i** **belastnings**utjämning väljer du **Azure Load Balancer**.
        - För **Välj en belastningsutjämnare**, *myLoadBalancer*. 
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**. 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Följ stegen för att skapa två ytterligare virtuella datorer – *myVM2* och *myVM3*, med en offentlig IP-adress för standard-SKU i **tillgänglighets zon** **2** respektive **3** , och alla andra inställningar är samma som *myVM1*.  

### <a name="create-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

I det här avsnittet skapar du en regel för nätverks säkerhets grupper för att tillåta inkommande anslutningar med HTTP.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myNetworkSecurityGroup** som finns i resurs gruppen för **myResourceGroupSLB** i listan resurser.
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
4. Välj **Lägg till**.

### <a name="install-iis-on-vms"></a>Installera IIS på virtuella datorer

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myVM1** som finns i resurs gruppen för *myResourceGroupSLB* i listan resurser.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. I popup-fönstret **Anslut till virtuell dator** väljer du **Hämta RDP-fil** och öppnar sedan den nedladdade RDP-filen.
4. Klicka på **Anslut** i fönstret **Anslutning till fjärrskrivbord**.
5. Logga in på den virtuella datorn med de autentiseringsuppgifter som du angav när du skapade den virtuella datorn. När du gör det startar en fjärrskrivbordssession med den virtuella datorn *myVM1*.
6. Navigera till **Windows Administrationsverktyg**>**Windows PowerShell** på serverdatorn.
7. I PowerShell-fönstret kör du följande kommandon för att installera IIS.servern, ta bort standardfilen iisstart.htm och lägga till en ny iisstart.htm-fil som visar namnet på den virtuella datorn:

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Stäng RDP-sessionen med *myVM1*.
7. Upprepa steg 1 till 6 för att installera IIS och den uppdaterade filen iisstart.htm på *myVM2* och *myVM3*.

## <a name="test-the-load-balancer"></a>Testa Load Balancer
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se Load Balancer distribuerar trafik mellan de tre virtuella datorerna som kör din app, kan du framtvinga uppdatering av webbläsaren.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Ta bort eller lägg till virtuella datorer från serverdelspoolen
Du kan behöva utföra underhåll på de virtuella datorerna som kör appen, till exempel installera uppdateringar av operativsystemet. För att klara ökad trafik till din app kan du behöva lägga till fler virtuella datorer. I det här avsnittet visas hur du tar bort eller lägger till en virtuell dator (*myVM1*) från Load Balancer.

### <a name="remove-vm-from-a-backend-pool"></a>Ta bort virtuell dator från en backend-pool
Utför följande steg för att ta bort *myVM1* från backend-poolen:

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och klicka sedan på **myLoadBalancer** i listan resurser.
2. Under **Inställningar** klickar du på **serverdelspooler** och sedan i listan med serverdelspoolerna, klickar du på **myBackendPool**.
3. På sidan **myBackendPool** för att ta bort *VM1* väljer du borttagnings ikonen i slutet av raden som visar *MyVM1*och klickar sedan på **Spara**.

När *myVM1* inte längre är i serverdelens adresspool, kan du utföra alla underhållsaktiviteter på *myVM1*, till exempel installera programuppdateringar. I avsaknad av *VM1*bal anse ras belastningen på *myVM2* och *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Lägg till en virtuell dator i en backend-pool
Utför följande steg för att lägga till *myVM1* tillbaka till backend-poolen:

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myVM1** i listan resurser.
2. Välj **nätverk**under **Inställningar**på sidan **VM1** .
3. På sidan **nätverk** väljer du fliken **belastnings utjämning** och väljer sedan **Lägg till belastnings utjämning**.
4. På sidan **Lägg till belastnings utjämning** gör du följande:
   1. För **belastnings Utjämnings alternativ**väljer du **Azure Load Balancer**.
   2. Välj *myLoadBalancer*för **Välj en belastningsutjämnare**.
   3. För **Välj en backend-pool**väljer du *myBackendPool*. 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, Load Balancer och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resurs gruppen *myResouceGroupSLB* som innehåller Load Balancer och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Standard Load Balancer anslutna virtuella datorer till den, konfigurerat Load Balancer trafik regel, hälso avsökning och sedan testat Load Balancer. Du har också tagit bort en virtuell dator från den belastningsutjämnade uppsättningen och lagt tillbaka den till serverdelsadresspoolen. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
