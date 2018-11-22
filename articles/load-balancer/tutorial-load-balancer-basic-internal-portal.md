---
title: 'Självstudier: Skapa en offentlig enkel lastbalanserare – Azure Portal | Microsoft Docs'
description: Den här självstudiekursen beskriver hur du skapar en intern, enkel lastbalanserare med hjälp av Azure Portal.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a5b6ae833fcd340a639c068156940e6b9ad469ca
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711998"
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Självstudier: Lastbalansera intern trafik till virtuella datorer med en enkel lastbalanserare med hjälp av Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att lastbalansera intern trafik till virtuella datorer med en enkel lastbalanserare. Den här kursen beskriver hur du skapar nätverksresurser, backend-servrar och en enkel intern lastbalanserare.

Om du vill kan du slutföra den här självstudien med [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) eller [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Klicka på **Nytt** > **Nätverk** > **Virtuella nätverk** överst till vänster på skärmen och ange följande värden för det virtuella nätverket:
    - *myVnet* – för det virtuella nätverkets namn.
    - *myResourceGroupILB* – namnet på den befintliga resursgruppen
    - *myBackendSubnet* – för undernätsnamnet.
2. Skapa det virtuella nätverket genom att klicka på **Skapa**.

![Skapa en lastbalanserare](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## <a name="create-a-basic-load-balancer"></a>Skapa en grundläggande lastbalanserare
Skapa en intern enkel lastbalanserare med hjälp av portalen.

1. Längst upp till vänster på skärmen klickar du på **Skapa en resurs** > **Nätverk** > **Lastbalanserare**.
2. Ange dessa värden för lastbalanseraren på sidan **Skapa en lastbalanserare**:
    - *myLoadBalancer* – namnet på lastbalanseraren.
    - **Internal** – typen av lastbalanserare.
    - **Basic** – SKU-versionen.
    - **10.1.0.7** – den statiska privata IP-adressen.
    - *myVNet* – det virtuella nätverk som du väljer i listan över befintliga nätverk.
    - *mySubnet* – det undernät som du väljer från listan med befintliga undernät.
    - *myResourceGroupILB* – namnet på den nya resursgrupp som du skapar.
3. Skapa lastbalanseraren genom att klicka på **Skapa**.
   
    ## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du två virtuella datorer för lastbalanserarens backend-pool och installerar sedan IIS på de virtuella datorerna. IIS gör det lättare att testa lastbalanseraren.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

1. Klicka på **Skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter** överst till vänster på skärmen och ange följande värden för den virtuella datorn:
    - *myVM1* – för den virtuella datorns namn.        
    - *azureuser* – för administratörens användarnamn.   
    - *myResourceGroupILB* – för **Resursgrupp**väljer du **Använd befintlig** och väljer sedan *myResourceGroupILB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    - *myAvailabilitySet* – för namnet på den nya tillgänglighetsuppsättning som du skapar.
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
5. Under **Nätverkssäkerhetsgrupp** väljer du **Avancerat**. För **Nätverkssäkerhetsgrupp (brandvägg)** väljer du sedan **Ingen**.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.
7. Gå igenom steg 1-6 och skapa en andra virtuell dator med namnet *VM2*, med *myAvailabilityset* som tillgänglighetsuppsättning, *myVnet* som virtuellt nätverk, *myBackendSubnet* som undernät och välj **Ingen** för alternativet **Nätverkssäkerhetsgrupp (brandvägg)**. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Installera IIS och anpassa standardwebbsidan

1. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVM1** som finns i resursgruppen *myResourceGroupILB*.
2. Klicka på **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
3. Logga in på den virtuella datorn.
4. Navigera till **Windows Administrationsverktyg**>**Serverhanteraren** på serverdatorn.
5. Starta Windows PowerShell på VM1 och kör följande kommandon för att installera IIS-servern och uppdatera standard-HTML-filen.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Stäng RDP-anslutningen med *myVM1*.
6. Upprepa steg 1–5 med *myVM2* för att installera IIS och anpassa standardwebbsidan.

## <a name="create-basic-load-balancer-resources"></a>Skapa en grundläggande lastbalanserare

I det här avsnittet konfigurerar du inställningarna för lastbalanseraren för en serverdelsadresspool och en hälsoavsökning och anger regler för lastbalanseraren.


### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

För att distribuera trafik till de virtuella datorerna finns en adresspool på serverdelen som innehåller IP-adresserna för de virtuella nätverkskort som är anslutna till lastbalanseraren. Skapa serverdelsadresspoolen *myBackendPool* så att den omfattar *VM1* och *VM2*.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Serverdelspooler** under **Inställningar** och klicka sedan på **Lägg till**.
3. Gör följande på sidan **Lägg till en serverdelspool**:
    - Ange *myBackEndPool* som namn på serverdelspoolen.
    - Klicka på **Tillgänglighetsuppsättning** för **Associerad till** i den nedrullningsbara menyn
    - Klicka på **myAvailabilitySet** för **Tillgänglighetsuppsättning**.
    - Klicka på **Lägg till en målnätverks-IP-konfiguration** och lägg till varje virtuell dator (*myVM1* & *myVM2*) som du skapade till serverdelspoolen.
    - Klicka på **OK**.

        ![Lägga till i backend-adresspoolen (serverdelspoolen) ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Kontrollera att inställningen för serverdelspoolens lastbalanserare visar båda de virtuella datorerna **VM1** och **VM2**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

Om du vill att den grundläggande lastbalanseraren ska övervaka status för din app kan du använda en hälsoavsökning. Hälsoavsökningen lägger till eller tar bort virtuella datorer dynamiskt från lastbalanserarens rotation baserat på deras svar på hälsokontroller. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
2. Klicka på **Hälsoavsökningar** under **Inställningar** och klicka sedan på **Lägg till**.
3. Använd följande värden när du skapar hälsoavsökningen:
    - *myHealthProbe* – för hälsoavsökningens namn.
    - **HTTP** – för protokolltypen.
    - *80* – för portnumret.
    - *15* – för antalet **intervall** i sekunder mellan avsökningsförsöken.
    - *2* – för antalet **tröskelvärden för ohälsosamt värde** eller antalet avsökningsfel i följd som måste inträffa innan en virtuell dator anses vara felaktig.
4. Klicka på **OK**.

   ![Lägga till en avsökning](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Skapa lastbalanseringsregeln *myLoadBalancerRuleWeb* så att du kan lyssna på port 80 i klientdelen *LoadBalancerFrontEnd* och skicka lastbalanserad nätverkstrafik till serverdelsadresspoolen *myBackEndPool* som också använder port 80. 

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
    
    ![Lägga till en belastningsutjämningsregel](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Skapa en virtuell dator för att testa lastbalanseraren
För att testa den interna lastbalanseraren måste du skapa en virtuell dator som finns i samma virtuella nätverk som de virtuella datorerna på backend-servern.
1. Klicka på **Skapa en resurs** > **Compute** > **Windows Server 2016 Datacenter** överst till vänster på skärmen och ange följande värden för den virtuella datorn:
    - *myVMTest* – den virtuella datorns namn.        
    - *myResourceGroupILB* – för **Resursgrupp**väljer du **Använd befintlig** och väljer sedan *myResourceGroupILB*.
2. Klicka på **OK**.
3. Välj **DS1_V2** som storlek på den virtuella datorn och klicka på **Välj**.
4. Ange dessa värden för VM-inställningarna:
    -  *myVNet* – se till att detta har markerats som det virtuella nätverket.
    - *myBackendSubnet* – kontrollera att det har markerats som undernätet.
5. Inaktivera startdiagnostikinställningar genom att klicka på **Inaktiverad**.
6. Klicka på **OK**, granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren
1. På Azure Portal hämtar du den privata-IP-adressen för lastbalanseraren på skärmen **Översikt**. Gör så här: a. Klicka på **Alla resurser** i den vänstra menyn och klicka sedan på **myLoadBalancer** i resurslistan.
    b. På detaljsidan för **Översikt** kopierar du den privata IP-adressen (i det här exemplet är det 10.1.0.7).

2. Skapa en fjärranslutning till *myVMTest* så här: a. Klicka på **Alla resurser** på den vänstra menyn och klicka sedan i resurslistan på **myVMTest** som finns i resursgruppen *myResourceGroupILB*.
2. Starta en fjärrsession med den virtuella datorn genom att klicka på **Anslut** på sidan **Översikt**.
3. Logga in på *myVMTest*.
3. Klistra in den privata IP-adressen i adressfältet i webbläsaren i *myVMTest*. IIS-webbserverns standardsida visas i webbläsaren.

      ![IIS-webbserver](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Om du vill se hur lastbalanseraren distribuerar trafik över båda virtuella datorer som kör din app, kan du framtvinga uppdatering av webbläsaren.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. Gör det genom att markera den resursgrupp som innehåller lastbalanseraren och sedan klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen skapade du en resursgrupp, nätverksresurser och backend-servrar. Sedan använde du dessa resurser för att skapa en intern lastbalanserare som lastbalanserar intern trafik till virtuella datorer. Lär dig hur du [belastningsutjämnar virtuella datorer mellan tillgänglighetszoner](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
