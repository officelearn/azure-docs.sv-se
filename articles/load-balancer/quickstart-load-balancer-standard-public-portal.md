---
title: 'Snabb start: skapa en offentlig Load Balancer-Azure Portal'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en Load Balancer med hjälp av Azure Portal.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78898839"
---
# <a name="quickstart-create-a-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabb start: skapa en Load Balancer för att belastningsutjämna virtuella datorer med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer (VM). Den här snabb starten visar hur du kan belastningsutjämna virtuella datorer med en offentlig Load Balancer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

I det här avsnittet skapar du en Load Balancer som hjälper till att belastningsutjämna virtuella datorer. Du kan skapa en offentlig Load Balancer eller en intern Load Balancer. När du skapar en offentlig Load Balancer måste du också skapa en ny offentlig IP-adress som är konfigurerad som klient del (med namnet as *LoadBalancerFrontend* som standard) för Load Balancer.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs** > **nätverk** > **Load Balancer**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och skriv *myResourceGroupSLB* i text rutan.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Välj **Europa, västra**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Välj **standard** eller **Basic**. Microsoft rekommenderar standard för produktions arbets belastningar. |
    | Offentlig IP-adress | Välj **Skapa ny**. Om du har en befintlig offentlig IP-adress som du vill använda väljer du **Använd befintlig** |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   Används ```-SKU Basic``` för att skapa en grundläggande offentlig IP-adress. Grundläggande offentliga IP-adresser är inte kompatibla med **standard** belastnings utjämning. Microsoft rekommenderar att du använder **standard** för produktions arbets belastningar.|
    | Tillgänglighetszon | Skriv *Zone-redundant* för att skapa en elastisk Load Balancer. Om du vill skapa en zonindelade Load Balancer väljer du en speciell zon från 1, 2 eller 3 |

> [!IMPORTANT]
> Resten av den här snabb starten förutsätter att **standard** -SKU väljs under urvals processen för SKU ovan.


3. På fliken **Granska och skapa** väljer du **skapa**.   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Skapa Load Balancer resurser

I det här avsnittet konfigurerar du Load Balancer inställningar för en backend-adresspool, en hälso avsökning och anger en balans regel.

### <a name="create-a-backend-pool"></a>Skapa en backend-pool

För att distribuera trafik till de virtuella datorerna innehåller en backend-adresspool IP-adresserna för de virtuella nätverkskorten som är anslutna till Load Balancer. Skapa *myBackendPool* för backend-adresspoolen för att inkludera virtuella datorer för belastnings utjämning av Internet trafik.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Under **Inställningar**väljer du **backend-pooler**och väljer sedan **Lägg till**.
3. På sidan **Lägg till en server dels grupp** anger du *myBackendPool*som namn på din backend-pool. Välj sedan **Lägg till**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill tillåta att Load Balancer övervakar appens status använder du en hälso avsökning. Hälso avsökningen lägger till eller tar bort virtuella datorer dynamiskt från Load Balancer rotation baserat på deras svar på hälso kontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Välj **hälso avsökningar**under **Inställningar**och välj sedan **Lägg till**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHealthProbe*. |
    | Protokoll | Välj **http**. |
    | Port | Ange *80*.|
    | Intervall | Ange *15* som **intervall** i sekunder mellan avsöknings försök. |
    | Tröskelvärde för ej felfri | Välj **2** för antalet fel i **tröskeln** eller på varandra följande avsöknings fel som måste inträffa innan en virtuell dator betraktas som ohälsosam.|
    | | |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa en Load Balancer regel *myLoadBalancerRuleWeb* för att lyssna på port 80 i klient delens *FrontendLoadBalancer* och skicka belastningsutjämnad nätverks trafik till backend-adresspoolen *myBackEndPool* även med port 80. 

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myLoadBalancer** i listan resurser.
2. Välj **belastnings Utjämnings regler**under **Inställningar**och välj sedan **Lägg till**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myHTTPRule*. |
    | Protokoll | Välj **TCP**. |
    | Port | Ange *80*.|
    | Backend-port | Ange *80*. |
    | Serverdelspool | Välj *myBackendPool*.|
    | Hälsoavsökning | Välj *myHealthProbe*. |
4. Lämna resten av standardinställningarna och välj sedan **OK**.


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du ett virtuellt nätverk, skapar tre virtuella datorer för den Load Balancerens backend-pool och installerar sedan IIS på de virtuella datorerna för att testa Load Balancer.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resurs grupp-namn>**  | myResourceGroupSLB |
| **\<virtuell-nätverks namn>** | myVNet          |
| **\<region namn>**          | Europa, västra      |
| **\<IPv4-adress utrymme>**   | 10.1.0.0 \ 16          |
| **\<under näts namn>**          | myBackendSubnet        |
| **\<undernät-adress – intervall>** | 10.1.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Offentliga IP-SKU: er och Load Balancer SKU: er måste matcha. För Standard Load Balancer använder du virtuella datorer med standard-IP-adresser i backend-poolen. I det här avsnittet ska du skapa tre virtuella datorer (*myVM1*, *myVM2* och *MyVM3*) med en offentlig standard-IP-adress i tre olika zoner (*zon 1*, *zon 2*och *Zon 3*) som senare läggs till i backend-poolen för Load Balancer som skapades tidigare. Om du har valt grundläggande använder du virtuella datorer med grundläggande IP-adresser.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs** > **Compute** > **Windows Server 2019 Data Center**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumerations** > **resurs grupp**: Välj **myResourceGroupSLB**.
   - **Instans information** > **namn på virtuell dator**: Skriv *myVM1*.
   - **Instans information** > **region** > Välj **Europa, västra**.
   - **Instans information** > **tillgänglighets alternativ** > Välj **tillgänglighets zoner**. 
   - **Instans information** > **tillgänglighets zon** > Välj **1**.
   - **Administratörs konto**> ange **användar namn**, **lösen ord** och **Bekräfta lösen ords** information.
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
1. På fliken **nätverk** ser du till att följande är markerat:
   - **Virtuellt nätverk**: *myVnet*
   - **Undernät**: *myBackendSubnet*
   - **Offentlig ip** > Välj **Skapa ny**och i fönstret **skapa offentlig IP-adress** för **SKU**väljer du **standard**och för **tillgänglighets zon**väljer du **zon-redundant**och väljer sedan **OK**. Om du har skapat en grundläggande Load Balancer väljer du Basic. Microsoft rekommenderar att du använder standard-SKU för produktions arbets belastningar.
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Skriv *myNetworkSecurityGroup*och välj **OK**.
   - Utför följande steg för att göra den virtuella datorn till en del av Load Balancers backend-pool:
        - I **belastnings utjämning**väljer du **Ja**för att **Placera den virtuella datorn bakom en befintlig belastnings Utjämnings lösning?**.
        - I inställningar **för belastnings utjämning i** **belastnings**utjämning väljer du **Azure Load Balancer**.
        - För **Välj en belastningsutjämnare**, *myLoadBalancer*.
        - Välj fliken **Hantering** eller **Nästa** > **Hantering**.
2. På fliken **hantering** , under **övervakning**, ställer du in **startdiagnostik** på **av**. 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Följ steg 2 till 6 för att skapa två ytterligare virtuella datorer med följande värden och alla andra inställningar på samma sätt som *myVM1*:

    | Inställningen | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  *myVM2* |*myVM3*|
    | Tillgänglighetszon | 2 |3|
    |Offentlig IP-adress| **Standard** SKU|**Standard** SKU|
    | Offentlig IP-tillgänglighets zon| **Zonen är redundant** |**Zonen är redundant**|
    | Nätverkssäkerhetsgrupp | Välj den befintliga *myNetworkSecurity-gruppen*| Välj den befintliga *myNetworkSecurity-gruppen*|

 ### <a name="create-nsg-rule"></a>Skapa NSG-regel

I det här avsnittet skapar du en regel för nätverks säkerhets grupper för att tillåta inkommande anslutningar med HTTP.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myNetworkSecurityGroup** som finns i resurs gruppen **myResourceGroupSLB** i listan resurser.
2. Under **Inställningar** väljer du **Inkommande säkerhetsregler** och sedan **Lägg till**.
3. Ange dessa värden för den ingående säkerhetsregeln *myHTTPRule* så att inkommande HTTP-anslutningar som använder port 80 tillåts:
    - **Källa**: *service tag*
    -  **Käll tjänst tag gen**: *Internet*
    - **Mål Port intervall**: *80*
    - **Protokoll**: *TCP*
    - **Åtgärd**: *Tillåt*
    - **Prioritet**: *100*
    - **Namn**: *myHTTPRule* 
    - **Beskrivning**: "*Tillåt http* 
4. Välj **Lägg till**.
5. Upprepa stegen för inkommande RDP-regel, om det behövs, med följande olika värden:
   - **Målportintervall**: Skriv *3389*.
   - **Prioritet**: Skriv *200*. 
   - **Namn**: Skriv *MyRDPRule*. 
   - **Beskrivning**: Skriv *Tillåt RDP*. 
 
### <a name="install-iis"></a>Installera IIS

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myVM1** i resurs gruppen *myResourceGroupSLB* i resurs gruppen.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
5. Logga in på den virtuella datorn med de autentiseringsuppgifter som du angav när du skapade den virtuella datorn. När du gör det startar en fjärrskrivbordssession med den virtuella datorn *myVM1*.
6. Gå till **Windows administrations verktyg**>**Windows PowerShell**på server Skriv bordet.
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
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser**och välj sedan **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Om du vill se Load Balancer distribuerar trafik över alla tre virtuella datorer kan du anpassa standard sidan för varje virtuell dators IIS-webbserver och sedan framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, Load Balancer och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du den resurs grupp (*myResourceGroupSLB*) som innehåller Load Balancer och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en Standard Load Balancer anslutna virtuella datorer till den, konfigurerat Load Balancer trafik regel, hälso avsökning och sedan testat Load Balancer. Om du vill veta mer om Azure Load Balancer fortsätter du till [Azure Load Balancer själv studie kurser](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

Läs mer om [Load Balancer-och tillgänglighets zoner](load-balancer-standard-availability-zones.md).
