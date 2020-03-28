---
title: 'Självstudiekurs: Belastningsutjämnad internettrafik till virtuella datorer - Azure-portal'
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
ms.openlocfilehash: 8961a50490bdbf8b456e87e1c00577c2c8afd050
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80240365"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>Självstudie: Belastningsutjämna internettrafik till virtuella datorer med hjälp av Azure-portalen

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. I den här självstudien får du lära dig mer om de olika komponenterna i den Azure Standard Load Balancer som distribuerar internettrafik till virtuella datorer och ger hög tillgänglighet. Lär dig att:


> [!div class="checklist"]
> * Skapa en Azure-belastningsutjämning
> * Skapa belastningsutjämningsresurser
> * Skapa virtuella datorer och installera IIS-servern
> * Visa belastningsutjämnare i åtgärd
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

I det här avsnittet skapar du en standardbelastningsutjämningsfaktor som hjälper till att belastningsutjämna virtuella datorer. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. 

1. Klicka på **Skapa en resurs** > **Nätverksbelastningsutjämnare****Networking** > längst upp till vänster på skärmen .
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa nytt** och skriv *myResourceGroupSLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **Standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |

3. På fliken **Granska + skapa** klickar du på **Skapa**.

   ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa belastningsutjämningsresurser

I det här avsnittet konfigurerar du belastningsutjämnadsinställningar för en serverdelsadresspool, en hälsoavsökning och anger en balancer-regel.

### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

Om du vill distribuera trafik till de virtuella datorerna innehåller en serverd-adresspool IP-adresserna för de virtuella (nasatorer) som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* för att inkludera virtuella datorer för belastningsutjämning av internettrafik.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Skriv *myBackendPool*för namn på sidan **Lägg till en backend-pool** och välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att belastningsutjämnaren ska kunna övervaka appens status använder du en hälsoavsökning. Hälsoavsökningen lägger dynamiskt till eller tar bort virtuella datorer från belastningsutjämnarrotationen baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
     
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHealthProbe*. |
    | Protokoll | Välj **HTTP**. |
    | Port | Skriv in *80*.|
    | Intervall | Ange *15* för antal **intervall** i sekunder mellan avsökningsförsök. |
    | Felfritt tröskelvärde | Välj *2* för antal **felaktiga tröskelvärden** eller på varandra följande avsökningsfel som måste inträffa innan en virtuell dator anses vara fel.|
    
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en belastningsutjämnad regel *myLoadBalancerRuleWeb* för att lyssna på port 80 i *frontend FrontendLoadBalancer* och skicka belastningsbalanserad nätverkstrafik till backend-adresspoolen *myBackEndPool* också med port 80.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Belastningsutjämningsregler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd dessa värden för att konfigurera belastningsutjämningsregeln:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *minHTTPRule*. |
    | Protokoll | Välj **TCP**. |
    | Port | Skriv in *80*.|
    | Backend-port | Skriv in *80*. |
    | Serverdelspool | Välj *myBackendPool*.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
    
4. Lämna resten av standardinställningarna och välj **OK**.

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, skapar tre virtuella datorer för serverdelspoolen för belastningsutjämnaren och installerar sedan IIS på de virtuella datorerna för att testa belastningsutjämnaren.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupSLB (Välj befintlig resursgrupp) |
| **\<>virtuellt nätverksnamn** | myVNet          |
| **\<regionnamn>**          | Europa, västra      |
| **\<IPv4-adress-utrymme>**   | 10.1.0.0/16          |
| **\<>i>**          | mySubnet        |
| **\<>för>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Standardbelastningsutjämnare stöder endast virtuella datorer med standard-IP-adresser i serverdapoolen. I det här avsnittet skapar du tre virtuella datorer *(myVM1*, *myVM2*och *myVM3*) med en offentlig standard-IP-adress i tre olika zoner *(zon 1*, *zon 2*och *zon 3*) som läggs till i backend-poolen för standardbelastningsutjämningsapparaten som skapades tidigare.

1. På portalens övre vänstra sida väljer du Skapa ett > **resursberäkningscenter** > för**Windows Server 2016**. **Create a resource** 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupSLB**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myVM1*.
   - **Instance Details** > Region för**instansinformation** > välja **Västeuropa**.
   - **Instance Details** > **Tillgänglighetsalternativ** för instansinformation > Välj **tillgänglighetszoner**. 
   - **Instance Details** > **Tillgänglighetszon** för instansinformation > Välj **1**.
  
1. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**. 
   
   - Kontrollera att följande har valts:
       - **Virtuellt nätverk:** **myVnet**
       - **Undernät**: **myBackendSubnet**
       - **Offentlig IP->** välja **Skapa ny**och i fönstret Skapa **offentlig IP-adress** för **SKU**väljer du **Standard**och för **tillgänglighetszon**väljer du **Zonlösenta**
      
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Skriv *myNetworkSecurityGroup*och välj **OK**.

   - Så här gör du den virtuella datorn till en del av belastningsutjämnarens backend-pool:
        - I **Belastningsutjämning** **väljer**du **Ja**.
        - I Inställningar för **belastningsutjämning**väljer du **Azure load balancer**för **inläsningsinställningar**.
        - För **Välj en belastningsutjämnare** *, myLoadBalancer*. 
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**. 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Följ stegen för att skapa ytterligare två virtuella datorer - *myVM2* och *myVM3*, med en vanlig SKU offentlig IP-adress i **tillgänglighet zon** **2** och **3 respektive,** och alla andra inställningar samma som *myVM1*.  

### <a name="create-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

I det här avsnittet skapar du en regel för nätverkssäkerhetsgrupp för att tillåta inkommande anslutningar med HTTP.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myNetworkSecurityGroup** som finns i resursgruppen **myResourceGroupSLB.**
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

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myVM1** i resursgruppen *myResourceGroupSLB* i resurslistan.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. I popup-fönstret **Anslut till virtuell dator** väljer du **Hämta RDP-fil** och öppnar sedan den nedladdade RDP-filen.
4. Klicka på **Anslut** i fönstret **Anslutning till fjärrskrivbord**.
5. Logga in på den virtuella datorn med de autentiseringsuppgifter som du angav när du skapade den virtuella datorn. När du gör det startar en fjärrskrivbordssession med den virtuella datorn *myVM1*.
6. På serverskrivbordet navigerar du till **Windows Administrationsverktyg**>**Windows PowerShell**.
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

## <a name="test-the-load-balancer"></a>Testa belastningsutjämnaren
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se belastningsutjämnaren distribuera trafik mellan de tre virtuella datorer som kör appen kan du tvinga fram uppdatering av webbläsaren.

## <a name="remove-or-add-vms-from-the-backend-pool"></a>Ta bort eller lägg till virtuella datorer från serverdelspoolen
Du kan behöva utföra underhåll på de virtuella datorerna som kör appen, till exempel installera uppdateringar av operativsystemet. För att klara ökad trafik till din app kan du behöva lägga till fler virtuella datorer. I det här avsnittet visas hur du tar bort eller lägger till en virtuell dator *(myVM1)* från belastningsutjämnaren.

### <a name="remove-vm-from-a-backend-pool"></a>Ta bort virtuell dator från en backend-pool
Så här tar du bort *myVM1* från backend-poolen:

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och klicka sedan på **myLoadBalancer** i resurslistan.
2. Under **Inställningar** klickar du på **serverdelspooler** och sedan i listan med serverdelspoolerna, klickar du på **myBackendPool**.
3. På **sidan myBackendPool** *markerar* du borttagningsikonen i slutet av raden som visar *myVM1*och klickar sedan på **Spara**.

När *myVM1* inte längre är i serverdelens adresspool, kan du utföra alla underhållsaktiviteter på *myVM1*, till exempel installera programuppdateringar. I avsaknad av *VM1*är belastningen nu balanserad över *myVM2* och *myVM3*. 

### <a name="add-vm-to-a-backend-pool"></a>Lägga till virtuell dator i en backend-pool
Så här lägger du till *myVM1* i backend-poolen:

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myVM1** i resurslistan.
2. Välj **Nätverk**under **Inställningar**på sidan **VM1.**
3. På sidan **Nätverk** väljer du fliken **Belastningsutjämning** och väljer sedan **Lägg till belastningsutjämning**.
4. Gör följande på sidan **Lägg till belastningsutjämning:**
   1. För **belastningsutjämningsalternativ**väljer du **Azure load balancer**.
   2. För **Välj en belastningsutjämnare**väljer du *myLoadBalancer*.
   3. För **Välj en backend-pool**väljer du *myBackendPool*. 

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs tar du bort resursgruppen, belastningsutjämnaren och alla relaterade resurser. Det gör du genom att välja den *myResouceGroupSLB-resursgrupp* som innehåller belastningsutjämnaren och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en standardbelastningsutjämnare, kopplade virtuella datorer till den, konfigurerade trafikregeln lastutjämning, hälsoavsökning och testade sedan belastningsutjämnaren. Du har också tagit bort en virtuell dator från den belastningsutjämnade uppsättningen och lagt tillbaka den till serverdelsadresspoolen. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
