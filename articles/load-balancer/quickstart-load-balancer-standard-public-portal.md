---
title: Snabbstart:Skapa en offentlig belastningsutjämnare - Azure-portal
titleSuffix: Azure Load Balancer
description: Den här snabbstarten visar hur du skapar en belastningsutjämnare med hjälp av Azure-portalen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b424fe315737b84479283eed2d77398c8ce4f148
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78898839"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabbstart: Skapa en belastningsutjämnare för att läsa in virtuella datorer med hjälp av Azure-portalen

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer (VM). Den här snabbstarten visar hur du laddar fördelning virtuella datorer med hjälp av en offentlig belastningsutjämnare.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet skapar du en belastningsutjämnare som hjälper till att belastningsutjämna virtuella datorer. Du kan skapa en offentlig belastningsutjämnare eller en intern belastningsutjämnare. När du skapar en offentlig belastningsutjämnare måste du också skapa en ny offentlig IP-adress som har konfigurerats som klientdel (som kallas *LoadBalancerFrontend* som standard) för belastningsutjämnaren.

1. Välj **Skapa en resurs** > **Nätverksbelastningsutjämnare****Networking** > längst upp till vänster på skärmen .
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa nytt** och skriv *myResourceGroupSLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **Standard** eller **Basic**. Microsoft rekommenderar Standard för produktionsarbetsbelastningar. |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP som du vill använda väljer du **Använd** |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   Används ```-SKU Basic``` för att skapa en grundläggande offentlig IP. Grundläggande offentliga IPs är **Standard** inte kompatibla med standardbelastningsutjämnare. Microsoft rekommenderar att du använder **Standard** för produktionsarbetsbelastningar.|
    | Tillgänglighetszon | Skriv *Zonundant* för att skapa en flexibel belastningsutjämnare. Om du vill skapa en zonindelningsbelastningsutdelningsapparat väljer du en viss zon från 1, 2 eller 3 |

> [!IMPORTANT]
> Resten av den här snabbstarten förutsätter att **Standard** SKU väljs under SKU-urvalsprocessen ovan.


3. Välj **Skapa**på fliken **Granska + skapa** .   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa belastningsutjämningsresurser

I det här avsnittet konfigurerar du belastningsutjämnadsinställningar för en serverdelsadresspool, en hälsoavsökning och anger en balancer-regel.

### <a name="create-a-backend-pool"></a>Skapa en backend-pool

Om du vill distribuera trafik till de virtuella datorerna innehåller en serverd-adresspool IP-adresserna för de virtuella (nasatorer) som är anslutna till belastningsutjämnaren. Skapa serverdelsadresspoolen *myBackendPool* för att inkludera virtuella datorer för belastningsutjämning av internettrafik.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Backend-pooler**och väljer sedan **Lägg till**.
3. Skriv *myBackendPool*för namn på sidan **Lägg till en backend-pool** och välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att belastningsutjämnaren ska kunna övervaka appens status använder du en hälsoavsökning. Hälsoavsökningen lägger dynamiskt till eller tar bort virtuella datorer från belastningsutjämnarrotationen baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Hälsoavsökningar**och väljer sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHealthProbe*. |
    | Protokoll | Välj **HTTP**. |
    | Port | Skriv in *80*.|
    | Intervall | Ange *15* för antal **intervall** i sekunder mellan avsökningsförsök. |
    | Felfritt tröskelvärde | Välj **2** för antal **felaktiga tröskelvärden** eller på varandra följande avsökningsfel som måste inträffa innan en virtuell dator anses vara fel.|
    | | |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en belastningsutjämnad regel *myLoadBalancerRuleWeb* för att lyssna på port 80 i *frontend FrontendLoadBalancer* och skicka belastningsbalanserad nätverkstrafik till backend-adresspoolen *myBackEndPool* också med port 80. 

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myLoadBalancer** i resurslistan.
2. Under **Inställningar**väljer du **Belastningsutjämningsregler**och väljer sedan **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *minHTTPRule*. |
    | Protokoll | Välj **TCP**. |
    | Port | Skriv in *80*.|
    | Backend-port | Skriv in *80*. |
    | Serverdelspool | Välj *myBackendPool*.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
4. Lämna resten av standardinställningarna och välj sedan **OK**.


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, skapar tre virtuella datorer för serverdelspoolen för belastningsutjämnaren och installerar sedan IIS på de virtuella datorerna för att testa belastningsutjämnaren.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupSLB |
| **\<>virtuellt nätverksnamn** | myVNet          |
| **\<regionnamn>**          | Europa, västra      |
| **\<IPv4-adress-utrymme>**   | 10.1.0.0\16          |
| **\<>i>**          | myBackendSubnet        |
| **\<>för>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Offentliga GPU:er och belastningsutjämnare SKU:er måste matcha. För Standard belastningsutjämnare använder du virtuella datorer med standard-IP-adresser i serverdapoolen. I det här avsnittet skapar du tre virtuella datorer *(myVM1*, *myVM2* och *myVM3*) med en offentlig standard-IP-adress i tre olika zoner *(zon 1*, *zon 2*och *zon 3*) som senare läggs till i backend-poolen för belastningsutjämnaren som skapades tidigare. Om du har valt Grundläggande använder du virtuella datorer med grundläggande IP-adresser.

1. På portalens övre vänstra sida väljer du Skapa ett > **resursberäkningscenter** > för**Windows Server 2019**. **Create a resource** 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupSLB**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv *myVM1*.
   - **Instance Details** > Region för**instansinformation** > välja **Västeuropa**.
   - **Instance Details** > **Tillgänglighetsalternativ** för instansinformation > Välj **tillgänglighetszoner**. 
   - **Instance Details** > **Tillgänglighetszon** för instansinformation > Välj **1**.
   - **Administratörskontot**> ange information **om användarnamn,** **lösenord** och **bekräfta lösenord.**
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
1. Kontrollera att följande är markerade på fliken **Nätverk:**
   - **Virtuellt nätverk:** *myVnet*
   - **Undernät**: *myBackendSubnet*
   - **Offentliga IP->** väljer **Skapa ny**och i fönstret Skapa offentlig **IP-adress** väljer **du** **Standard**för **zonen Tillgänglighet**och väljer sedan **Zonundant**och väljer sedan **OK**. Om du har skapat en grundläggande belastningsutjämnare väljer du Grundläggande. Microsoft rekommenderar att du använder Standard SKU för produktionsarbetsbelastningar.
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Skriv *myNetworkSecurityGroup*och välj **OK**.
   - Så här gör du den virtuella datorn till en del av belastningsutjämnarens backend-pool:
        - I **Belastningsutjämning** **väljer**du **Ja**.
        - I Inställningar för **belastningsutjämning**väljer du **Azure load balancer**för **inläsningsinställningar**.
        - För **Välj en belastningsutjämnare** *, myLoadBalancer*.
        - Välj fliken **Hantering** eller **Nästa** > **Hantering**.
2. Ange **Startdiagnostik** på **Av**under **Övervakning**på fliken **Hantering** . 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Följ stegen 2 till 6 för att skapa ytterligare två virtuella datorer med följande värden och alla andra inställningar på samma sätt som *myVM1:*

    | Inställning | VM 2| VM 3|
    | ------- | ----- |---|
    | Namn |  *myVM2 (på väg mot andra)* |*myVM3 (på väg)*|
    | Tillgänglighetszon | 2 |3|
    |Offentlig IP-adress| **Standard** Sku|**Standard** Sku|
    | Offentlig IP - Tillgänglighetszon| **Zon redundant** |**Zon redundant**|
    | Nätverkssäkerhetsgrupp | Välj den befintliga *myNetworkSecurity-gruppen*| Välj den befintliga *myNetworkSecurity-gruppen*|

 ### <a name="create-nsg-rule"></a>Skapa NSG-regel

I det här avsnittet skapar du en regel för nätverkssäkerhetsgrupp för att tillåta inkommande anslutningar med HTTP.

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myNetworkSecurityGroup** i resursgruppen i resursgruppen **myResourceGroupSLB.**
2. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - **Källa**: *Service Tag*
    -  **Källa tjänst tag:** *Internet*
    - **Destinationshamnsområden:** *80*
    - **Protokoll**: *TCP*
    - **Åtgärd**: *Tillåt*
    - **Prioritet**: *100*
    - **Namn**: *minHTTPRule* 
    - **Beskrivning**: "*Tillåt HTTP* 
4. Välj **Lägg till**.
5. Upprepa stegen för den inkommande RDP-regeln, om det behövs, med följande olika värden:
   - **Målportintervall**: Skriv *3389*.
   - **Prioritet**: Skriv *200*. 
   - **Namn**: Skriv *MyRDPRule*. 
   - **Beskrivning**: Skriv *Tillåt RDP*. 
 
### <a name="install-iis"></a>Installera IIS

1. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myVM1** i resursgruppen *myResourceGroupSLB* i resurslistan.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
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
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **Alla tjänster** på menyn till vänster, välj Alla **resurser**och välj sedan **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se belastningsutjämnaren distribuera trafik mellan alla tre virtuella datorer kan du anpassa standardsidan för varje vm-webbserver och sedan tvinga uppdatera webbläsaren från klientdatorn.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, belastningsutjämnaren och alla relaterade resurser. Om du vill göra det markerar du den resursgrupp *(myResourceGroupSLB)* som innehåller belastningsutjämnaren och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en standardbelastningsutjämningsapparat, kopplade virtuella datorer till den, konfigurerade trafikregeln lastutjämning, hälsoavsökning och testade sedan belastningsutjämnaren. Om du vill veta mer om Azure Load Balancer fortsätter du till [Azure Load Balancer-självstudier](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Läs mer om [belastningsutjämnare och tillgänglighetszoner](load-balancer-standard-availability-zones.md).
