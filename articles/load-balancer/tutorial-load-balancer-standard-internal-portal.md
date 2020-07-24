---
title: 'Självstudie: skapa en intern belastningsutjämnare – Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här självstudien visar hur du skapar en intern standard belastningsutjämnare med hjälp av Azure Portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2020
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: f7f16093074b48610c1db8fec7f05ee01e7ab1ed
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078776"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-standard-load-balancer-in-the-azure-portal"></a>Självstudie: utjämna intern trafik belastning med en standard belastningsutjämnare i Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande begäranden sprids över virtuella datorer. Du kan använda Azure Portal för att skapa en standard belastningsutjämnare och utjämna intern trafik mellan virtuella datorer. Den här självstudien visar hur du skapar och konfigurerar en intern belastningsutjämnare, backend-servrar och nätverks resurser på standard pris nivån.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

Om du vill kan du utföra de här stegen med hjälp av [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) eller [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) i stället för portalen.

Om du vill utföra stegen i den här självstudien loggar du in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar
I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupSLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA, östra 2      |
| **\<IPv4-address-space>**   | 10.3.0.0 \ 16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]
   


## <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **Compute**  >  **Windows Server 2016 Data Center**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumeration**  >  **Resurs grupp**: list rutan och välj **MyResourceGroupLB**.
   - **Instans information**  >  **Namn på virtuell dator**: Skriv **MyVM1**.
   - **Instans information**  >  **Region**: Välj **USA, östra 2**.
  
   
1. Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**. 
   
   Kontrollera att följande har valts:
   - **Virtuellt nätverk**: **MyVNet**
   - **Undernät**: **MyBackendSubnet**
   - **NIC-nätverks säkerhets grupp**: Välj **Basic**.
   - **Offentlig IP** > Välj **Skapa ny** och ange följande värden och välj **OK**:
       - **Namn**: **MyVM1-IP**
       - **SKU**: Välj **standard**
   - **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: list rutan och välj **RDP (3389)**

   
   
1. Välj fliken **Hantering** eller **Nästa** > **Hantering**. Under **Övervakning** anger du **Startdiagnostik** till **Av**.
   
1. Välj **Granska + skapa**.
   
1. Granska inställningarna och välj sedan **Skapa**. 

1. Följ stegen för att skapa en andra virtuell dator med namnet **MyVM2**, med alla andra inställningar på samma sätt som MyVM1. 

1. Följ stegen igen för att skapa en tredje virtuell dator med namnet **MyTestVM**. 

## <a name="create-a-standard-load-balancer"></a>Skapa en standardlastbalanserare

Skapa en intern standard belastnings utjämning med hjälp av portalen. Det namn och den IP-adress som du skapar konfigureras automatiskt som lastbalanserarens klientdel.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **nätverk**  >  **Load Balancer**.
   
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *MyResourceGroupLB* i textrutan.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **USA, östra 2**.                                        |
    | Typ          | Välj **Intern**.                                        |
    | SKU           | Välj **standard**.                          |
    | Virtuellt nätverk           | Välj *MyVNet*.                          |    
    | Tilldelning av IP-adress              | Välj **Statisk**.   |
    | Privat IP-adress|Skriv en adress som är i adressrymden för ditt virtuella nätverk och undernät, till exempel *10.3.0.7*.  |

3. På fliken **Granska + skapa** klickar du på **Skapa**. 
   

## <a name="create-standard-load-balancer-resources"></a>Skapa standard resurser för belastnings utjämning

I det här avsnittet konfigurerar du inställningar för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning samt anger regler för lastbalanseraren.

### <a name="create-a-back-end-address-pool"></a>Skapa en backend-adresspool

Lastbalanseraren använder en serverdelsadresspool för att distribuera trafik till de virtuella datorerna. Serverdelsadresspoolen innehåller IP-adresserna för de gränssnitt för virtuella nätverk (NIC) som är anslutna till lastbalanseraren. 

**Så här skapar du en serverdelsadresspool som innehåller VM1 och VM2:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Serverdelspooler** och sedan **Lägg till**.
   
1. På sidan **Lägg till serverdelspool** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv **MyBackendPool**.
   
1. Under **virtuella datorer**. 
   1. Lägg till **MyVM1** och **MyVM2** till serverdelspoolen.
   2. När du har lagt till varje dator öppnar du listrutan och väljer dess **Nätverks-IP-konfiguration**. 
     
1. Välj **Lägg till**.
   
   ![Lägga till serverdelsadresspoolen](./media/tutorial-load-balancer-standard-internal-portal/3-load-balancer-backend-02.png)
   
1. På sidan **Serverdelspooler** expanderar du **MyBackendPool** och kontrollerar att både **VM1** och **VM2** visas.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att lastbalanseraren ska övervaka VM-status använder du en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. 

**Så här skapar du en hälsoavsökning för att övervaka de virtuella datorernas hälsa:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Hälsoavsökningar** och sedan **Lägg till**.
   
1. På sidan **Lägg till en hälsoavsökning ** skriver eller väljer du följande värden:
   
   - **Namn**: Skriv **MyHealthProbe**.
   - **Protokoll**: I listrutan väljer du **HTTP**. 
   - **Port**: Skriv **80**. 
   - **Sökväg**: Acceptera **/** för standard-URI. Du kan ersätta det här värdet med valfri URI. 
   - **Intervall**: Skriv **15**. Intervallet är antalet sekunder mellan avsökningsförsök.
   - **Tröskelvärde för Ej felfri**: Skriv **2**. Det här värdet är det antal avsökningsfel i följd som ska inträffa innan en virtuell dator anses vara felaktig.
   
1. Välj **OK**.
   
   ![Lägga till en avsökning](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel definierar hur trafiken ska distribueras till de virtuella datorerna. Regeln definierar IP-konfigurationen på klientdelen för inkommande trafik, serverdels-IP-poolen för att ta emot trafik samt nödvändiga käll- och målportar. 

Lastbalanserarregeln med namnet **MyLoadBalancerRule** avlyssnar port 80 i klientdelen **LoadBalancerFrontEnd**. Regeln skickar nätverkstrafik till serverdelsadresspoolen **MyBackendPool**, även det med port 80. 

**Så här skapar du lastbalanserarregeln:**

1. Välj **Alla resurser** på den vänstra menyn och välj sedan **MyLoadBalancer** i resurslistan.
   
1. Under **Inställningar** väljer du **Belastningsutjämningsregler** och väljer sedan **Lägg till**.
   
1. På sidan **Lägg till belastningsutjämningsregel** skriver eller väljer du följande värden om de inte redan finns:
   
   - **Namn**: Skriv **MyLoadBalancerRule**.
   - **Klientdels-IP-adress:** Skriv **LoadBalancerFrontend** om det inte finns.
   - **Protokoll**: Välj **TCP**.
   - **Port**: Skriv **80**.
   - **Serverdelsport**: Skriv **80**.
   - **Serverdelspool**: Välj **MyBackendPool**.
   - **Hälsoavsökning**: Välj **MyHealthProbe**. 
   
Om du vill konfigurera [portar med hög tillgänglighet](load-balancer-ha-ports-overview.md) med hjälp av Azure Portal väljer du kryss rutan **ha portar** . När det här alternativet väljs fylls den relaterade porten och protokoll konfigurationen i automatiskt. 

1. Välj **OK**.
   
   ![Lägga till en regel för lastbalanseraren](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

Installera Internet Information Services (IIS) på serverdelsservrarna och använd sedan MyTestVM för att testa lastbalanseraren med hjälp av dess privata IP-adress. Varje virtuella serverdelsdator arbetar med olika versioner av IIS-standardwebbplatsen, så du kan se när lastbalanseraren distribuerar begäranden mellan de två virtuella datorerna.

På portalen går du till sidan **Översikt** för **MyLoadBalancer** och letar upp dess IP-adress under **Privat IP-adress**. Hovra över adressen och kopiera den genom att välja ikonen **Kopiera**. I det här exemplet är den **10.3.0.7**. 

### <a name="connect-to-the-vms-with-rdp"></a>Ansluta till de virtuella datorerna med RDP

Anslut först till alla tre virtuella datorer med Fjärrskrivbord (RDP). 

>[!NOTE]
>Som standard har de virtuella datorerna redan porten för **RDP** (Fjärrskrivbord) öppen för att tillåta fjärrskrivbordsåtkomst. 

**Så här ansluter du via fjärrskrivbord (RDP) till de virtuella datorerna:**

1. På portalen väljer du **Alla resurser** på menyn till vänster. I resurslistan väljer du varje virtuell dator i resursgruppen **MyResourceGroupLB**.
   
1. På sidan **Översikt** väljer du **Anslut** och sedan **Ladda ned RDP-fil**. 
   
1. Öppna den RDP-fil som du laddade ned och välj **Anslut**.
   
1. På skärmen Windows-säkerhet väljer du **Fler alternativ** och sedan **Använd ett annat konto**. 
   
   Ange ett användarnamn och ett lösenord, och välj sedan **OK**.
   
1. Svara **Ja** på eventuella certifikatfrågor. 
   
   Den virtuella datorns skrivbord öppnas i ett nytt fönster. 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>Installera IIS och ersätta IIS-standardsidan på de virtuella serverdelsdatorerna

På varje serverdelsserver använder du PowerShell för att installera IIS och ersätter IIS-standardwebbplatsen med en anpassad sida.

>[!NOTE]
>Du kan även använda **guiden Lägg till roller och funktioner** i **Serverhanteraren** för att installera IIS. 

**Så här installerar du IIS och uppdaterar standardwebbplatsen med PowerShell:**

1. På MyVM1 och MyVM2 startar du **Windows PowerShell** från **Start-menyn**. 

2. Kör följande kommandon för att installera IIS och ersätta IIS-standardwebbplatsen:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
      remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add custom htm file
      Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Stäng RDP-anslutningarna med MyVM1 och MyVM2 genom att välja **Koppla från**. Stäng inte av de virtuella datorerna.

### <a name="test-the-load-balancer"></a>Testa lastbalanseraren

1. På MyTestVM öppnar du **Internet Explorer** och svarar **OK** på eventuella konfigurationsfrågor. 
   
1. Klistra in eller skriv lastbalanserarens privata IP-adress (*10.3.0.7*) i webbläsarens adressfält. 
   
   Den anpassade IIS-webbserverns standardsida visas i webbläsaren. I meddelandet står det antingen **Hello World from MyVM1** (Hej världen från MyVM1) eller **Hello World from MyVM2**.
   
1. Uppdatera webbläsaren om du vill se hur lastbalanseraren distribuerar trafik över virtuella datorer. Du kan även behöva rensa webbläsarens cache mellan försöken.

   Ibland visas sidan **MyVM1**, och vid andra tillfällen visas sidan **MyVM2** när lastbalanseraren distribuerar begäranden till varje virtuell serverdelsdator. 

   ![Ny IIS-standardsida](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort lastbalanseraren och alla relaterade resurser när du inte längre behöver dem öppnar du resursgruppen **MyResourceGroupLB** och väljer **Ta bort resursgrupp**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en standard intern belastningsutjämnare. Du skapade och konfigurerade nätverksresurser, serverdelsservrar, en hälsoavsökning och regler för lastbalanseraren. Du installerade IIS på virtuella serverdelsdatorer och använde en virtuell testdator för att testa lastbalanseraren i webbläsaren. 

Härnäst kan du lära dig att belastningsutjämna virtuella datorer mellan tillgänglighetszoner.

> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
