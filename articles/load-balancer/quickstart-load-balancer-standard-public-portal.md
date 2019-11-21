---
title: 'Snabbstart: Skapa en Standard Load Balancer – Azure-portalen'
titleSuffix: Azure Load Balancer
description: This quickstart shows how to create a Standard Load Balancer by using the Azure portal.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create a Standard Load Balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: d15223dfe6d9ce710f2a3d402a49203ef169132e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225195"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Snabbstart: Skapa en Standard Load Balancer som lastbalanserar virtuella datorer med Azure Portal

Med belastningsutjämning får du högre tillgänglighet och skala genom att inkommande förfrågningar sprids över flera virtuella datorer. Du kan använda Azure Portal för att skapa en lastbalanserare som lastbalanserar virtuella datorer (VM). Den här snabbstartsguiden visar hur du lastbalanserar virtuella datorer med en Standard Load Balancer.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Skapa en Standard Load Balancer

In this section, you create a Standard Load Balancer that helps load balance virtual machines. En Standard Load Balancer stöder endast offentliga IP-standardadresser. När du skapar en Standard Load Balancer, och även måste skapa en ny offentlig IP-standardadress som är konfigurerad som klientdelen (med namnet *LoadBalancerFrontend* som standard) för Standard Load Balancer. 

1. On the top left-hand side of the screen, select **Create a resource** > **Networking** > **Load Balancer**.
2. På fliken **Grundläggande inställningar** på sidan **Skapa lastbalanserare** anger eller väljer du följande information, accepterar standardinställningarna för de återstående inställningarna och väljer sedan **Granska + skapa**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Select **Create new** and type *myResourceGroupSLB* in the text box.|
    | Namn                   | *myLoadBalancer*                                   |
    | Region         | Välj **Västeuropa**.                                        |
    | Typ          | Välj **Offentligt**.                                        |
    | SKU           | Select **Standard**.                          |
    | Offentlig IP-adress | Välj **Skapa ny**. |
    | Namn på offentlig IP-adress              | Skriv *myPublicIP* i textrutan.   |
    |Tillgänglighetszon| Välj **Zonredundant**.    |
3. In the **Review + create** tab, select **Create**.   

    ![Skapa en Standard Load Balancer](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Create Load Balancer resources

In this section, you configure Load Balancer settings for a backend address pool, a health probe, and specify a balancer rule.

### <a name="create-a-backend-address-pool"></a>Skapa en serverdelsadresspool

To distribute traffic to the VMs, a backend address pool contains the IP addresses of the virtual (NICs) connected to the Load Balancer. Create the backend address pool *myBackendPool* to include virtual machines for load-balancing internet traffic.

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Backend pools**, then select **Add**.
3. On the **Add a backend pool** page, for name, type *myBackendPool*, as the name for your backend pool, and then select **Add**.

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

To allow the Load Balancer to monitor the status of your app, you use a health probe. The health probe dynamically adds or removes VMs from the Load Balancer rotation based on their response to health checks. Skapa en hälsoavsökning *myHealthProbe* så att du kan övervaka de virtuella datorernas hälsotillstånd.

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Health probes**, then select **Add**.
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Enter *myHealthProbe*. |
    | Protokoll | Select **HTTP**. |
    | Port | Enter *80*.|
    | Intervall | Enter *15* for number of **Interval** in seconds between probe attempts. |
    | Unhealthy threshold | Select **2** for number of **Unhealthy threshold** or consecutive probe failures that must occur before a VM is considered unhealthy.|
    | | |
4. Välj **OK**.

### <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel
En lastbalanseringsregel används för att definiera hur trafiken ska distribueras till de virtuella datorerna. Du definierar IP-konfigurationen på klientdelen för inkommande trafik och IP-poolen på serverdelen för att ta emot trafik samt nödvändig käll- och målport. Create a Load Balancer rule *myLoadBalancerRuleWeb* for listening to port 80 in the frontend *FrontendLoadBalancer* and sending load-balanced network traffic to the backend address pool *myBackEndPool* also using port 80. 

1. Select **All services** in the left-hand menu, select **All resources**, and then select **myLoadBalancer** from the resources list.
2. Under **Settings**, select **Load balancing rules**, then select **Add**.
3. Använd följande värden när du konfigurerar belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Enter *myHTTPRule*. |
    | Protokoll | Select **TCP**. |
    | Port | Enter *80*.|
    | Backend port | Enter *80*. |
    | Backend pool | Select *myBackendPool*.|
    | Hälsoavsökning | Select *myHealthProbe*. |
4. Leave the rest of the defaults and then select **OK**.


## <a name="create-backend-servers"></a>Skapa serverdelsservrar

In this section, you create a virtual network, create three virtual machines for the backend pool of the Load Balancer, and then install IIS on the virtual machines to help test the Load Balancer.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
1. Längst upp till vänster på skärmen väljer du **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.

1. I **Skapa virtuellt nätverk** anger eller väljer du följande information:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange *myVNet*. |
    | Adressutrymme | Ange *10.1.0.0/16*. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Select existing resource - *myResourceGroupSLB*. |
    | Plats | Välj **Västeuropa**.|
    | Undernät – namn | Ange *myBackendSubnet*. |
    | Undernät – adressintervall | Ange *10.1.0.0/24*. |
1. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer
Standard Load Balancer only supports VMs with Standard IP addresses in the backend pool. In this section, you will create three VMs (*myVM1*, *myVM2* and *myVM3*) with a Standard public IP address in three different zones (*Zone 1*, *Zone 2*, and *Zone 3*) that are later added to the backend pool of the Standard Load Balancer that was created earlier.

1. On the upper-left side of the portal, select **Create a resource** > **Compute** > **Windows Server 2019 Datacenter**. 
   
1. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Subscription** > **Resource Group**: Select **myResourceGroupSLB**.
   - **Instance Details** > **Virtual machine name**: Type *myVM1*.
   - **Instance Details** > **Region** > select **West Europe**.
   - **Instance Details** > **Availability Options** > Select **Availability zones**. 
   - **Instance Details** > **Availability zone** > Select **1**.
   - **Administrator account**> Enter the **Username**, **Password** and **Confirm password** information.
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.
  
1. In the **Networking** tab make sure the following are selected:
   - **Virtual network**: *myVnet*
   - **Subnet**: *myBackendSubnet*
   - **Public IP** > select **Create new**, and in the **Create public IP address** window, for **SKU**, select **Standard**, and for **Availability zone**, select **Zone-redundant**, and then select **OK**.
   - För att skapa en ny nätverkssäkerhetsgrupp (NSG), en typ av brandvägg, går du till **Nätverkssäkerhetsgrupp** och väljer **Avancerat**. 
       1. I fältet **Konfigurera nätverkssäkerhetsgrupp** väljer du **Skapa ny**. 
       1. Type *myNetworkSecurityGroup*, and select **OK**.
   - To make the VM a part of the Load Balancer's backend pool, complete the following steps:
        - In **Load Balancing**, for **Place this virtual machine behind an existing load balancing solution?** , select **Yes**.
        - In **Load balancing settings**, for **Load balancing options**, select **Azure load balancer**.
        - For **Select a load balancer**, *myLoadBalancer*.
        - Välj fliken **Hantering** eller **Nästa** > **Hantering**.
2. In the **Management** tab, under **Monitoring**, set **Boot diagnostics** to **Off**. 
1. Välj **Granska + skapa**.   
1. Granska inställningarna och välj sedan **Skapa**.
1. Follow the steps 2 to 6 to create two additional VMs with the following values and all the other settings the same as *myVM1*:

    | Inställning | VM 2| VM 3|
    | ------- | ----- |---|
    | Namn |  *myVM2* |*myVM3*|
    | Tillgänglighetszon | 2 |3|
    |Offentlig IP-adress| **Standard** SKU|**Standard** SKU|
    | Public IP - Availability zone| **Zone redundant** |**Zone redundant**|
    | Nätverkssäkerhetsgrupp | Select the existing *myNetworkSecurity Group*| Select the existing *myNetworkSecurity Group*|

 ### <a name="create-nsg-rule"></a>Skapa NSG-regel

In this section, you create a network security group rule to allow inbound connections using HTTP.

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list select **myNetworkSecurityGroup** that is located in the **myResourceGroupSLB** resource group.
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
4. Välj **Lägg till**.
 
### <a name="install-iis"></a>Installera IIS

1. Select **All services** in the left-hand menu, select **All resources**, and then from the resources list, select **myVM1** that is located in the *myResourceGroupSLB* resource group.
2. Välj **Anslut** på sidan **Översikt** och anslut RDP till den virtuella datorn.
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

## <a name="test-the-load-balancer"></a>Test the Load Balancer
1. Hitta den offentliga IP-adressen för lastbalanseraren på skärmen **Översikt**. Select **All services** in the left-hand menu, select **All resources**, and then select **myPublicIP**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

   ![IIS-webbserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

To see the Load Balancer distribute traffic across all three VMs, you can customize the default page of each VM's IIS Web server and then force-refresh your web browser from the client machine.

## <a name="clean-up-resources"></a>Rensa resurser

When no longer needed, delete the resource group, Load Balancer, and all related resources. To do so, select the resource group (*myResourceGroupSLB*) that contains the Load Balancer, and then select **Delete**.

## <a name="next-steps"></a>Nästa steg

In this quickstart, you created a Standard Load Balancer, attached VMs to it, configured the Load Balancer traffic rule, health probe, and then tested the Load Balancer. Om du vill läsa mer om Azure Load Balancer fortsätter du till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
