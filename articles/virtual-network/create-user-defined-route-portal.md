---
title: Skapa en användardefinierad väg väg trafik via en virtuell nätverksenhet - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar en användardefinierad väg för att åsidosätta standardvärdet för Azures routning av Routning av nätverkstrafik via en virtuell nätverksenhet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 0319029277091611673f15c94604604850cbfcbe
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2018
ms.locfileid: "29766283"
---
# <a name="create-a-user-defined-route---azure-portal"></a>Skapa en användardefinierad väg - Azure-portalen

I den här kursen lär du dig hur du skapar användardefinierade vägar för att vidarebefordra trafik mellan två [virtuellt nätverk](virtual-networks-overview.md) undernät via en virtuell nätverksenhet. En virtuell nätverksenhet är en virtuell dator som kör ett nätverksprogram, till exempel en brandvägg. Mer information om förkonfigurerade virtuella nätverksenheter som du kan distribuera i Azure-nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

När du skapar undernät i ett virtuellt nätverk, Azure skapar standard [systemvägar](virtual-networks-udr-overview.md#system-routes) som gör att resurser i alla undernät för att kommunicera med varandra, enligt följande bild:

![Standardvägar](./media/create-user-defined-route/default-routes.png)

I de här självstudierna skapar du ett virtuellt nätverk med offentliga och privata undernät till perimeternätverk, som visas i bilden nedan. Vanligtvis webbservrar kan distribueras till en offentlig undernät och ett program eller en databasserver kan distribueras till ett privat undernät. Du skapar en virtuell dator ska fungera som en virtuell nätverksenhet i DMZ-undernät och du kan också skapa en virtuell dator i varje undernät som kommunicerar via nätverket virtuell installation. All trafik mellan de offentliga och privata undernäten dirigeras via installationen som visas i följande bild:

![Användardefinierade vägar](./media/create-user-defined-route/user-defined-routes.png)

Den här artikeln innehåller steg för att skapa en användardefinierad väg genom distributionsmodell hanteraren för filserverresurser, som är den distributionsmodell som vi rekommenderar att du använder när du skapar användardefinierade vägar. Om du behöver skapa en användardefinierad väg (klassisk), se [skapa en användardefinierad väg (klassisk)](virtual-network-create-udr-classic-ps.md). Om du inte är bekant med Azures distributionsmodeller [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Mer information om användardefinierade vägar finns i [översikten över användardefinierade vägar](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Skapa vägar och virtuell nätverksenhet

1. **Nödvändiga**: skapa ett virtuellt nätverk med två undernät genom att slutföra stegen i [skapa ett virtuellt nätverk](#create-a-virtual-network).
2. När du har skapat det virtuella nätverket i en webbläsare kan du gå till den [Azure-portalen](https://portal.azure.com). Logga in med ditt [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. På den **söka resurser** överst i portalen och ange *myResourceGroup*. Klicka på **myResourceGroup** när den visas i sökresultaten. Resursgruppen har skapats som en del av nödvändiga.
4. Klicka på **myVnet**, enligt följande bild:

    ![Inställningar för nätverksgränssnitt](./media/create-user-defined-route/virtual-network.png)

5. Skapa ett undernät för virtuell nätverksenhet:
 
    - Under **myVnet**, klickar du på **undernät** under **inställningar** på vänster sida.
    - Klicka på **+ undernät**som visas i följande bild:

        ![Undernät](./media/create-user-defined-route/subnets.png) 
    - Ange följande värden under **Lägg till undernät**, klicka på **OK**:

        |Inställning|Värde|
        |-----|-----|
        |Namn|DMZ|
        |Adressintervall (CIDR-block)|10.0.2.0/24|

6. Skapa en virtuell dator för virtuell installation av nätverk:

    - Klicka på vänster sida av portalen **+ ny**, klicka på **Compute**, och klicka sedan på **Windows Server 2016 Datacenter** eller **Ubuntu Server 16.04 LTS**.
    - Ange följande värden den **grunderna** bladet som visas, klicka sedan på **OK**.

        |Inställning|Värde|
        |---|---|
        |Namn|myVm-Nva|
        |Användarnamn|azureuser|
        |Lösenord och Bekräfta lösenord|Ett lösenord som du väljer|
        |Prenumeration|Välj din prenumeration|
        |Resursgrupp|Klicka på **använda befintliga**och välj **myResourceGroup**|
        |Plats|Östra USA|
    - På den **välja en storlek** bladet som visas, klickar du på **DS1_V2 Standard**, klicka på **Välj**.
    - På den **inställningar** bladet som visas, klickar du på **för virtuella nätverk**. Klicka på **myVnet** i den **Välj virtuellt nätverk** bladet som visas.
    - På den **inställningar** bladet, klickar du på **undernät**. Klicka på **DMZ** på den **Välj undernät** bladet som visas. 
    - Lämnar du standardinställningarna för de återstående inställningarna och klickar på **OK**.
    - Klicka på **skapa** på den **skapa** bladet som visas. Distribution av den virtuella datorn tar några minuter.

    > [!NOTE]
    > Förutom att skapa virtuella Azure-portalen skapar en offentlig IP-adress och tilldelar den till den virtuella datorn som standard. Om du distribuerat den virtuella datorn i en produktionsmiljö, kan du inte tilldela en offentlig IP-adress till den virtuella datorn. I stället kan du komma åt virtuell nätverksenhet från andra virtuella datorer i det virtuella nätverket. Läs mer om offentliga IP-adresser i [hantera en offentlig IP-adress](virtual-network-public-ip-address.md).

7. Tilldela en statisk privat IP-adress och aktivera IP-vidarebefordring för nätverksgränssnittet portalen skapade i föregående steg. 
    - På den **söka resurser** överst på sidan, ange *myVm Nva*.
    - Klicka på **myVm Nva** när den visas i sökresultaten.
    - Klicka på **nätverk** under **inställningar** på vänster sida.
    - Klicka på namnet på nätverksgränssnittet under **myVm Nva - nätverksgränssnitt**. Namnet är **myvm nva *** X*, där *X* är ett tal som tilldelats av portalen.
    - Klicka på **IP-konfigurationer** under **inställningar** för nätverksgränssnittet, enligt följande bild:

        ![Inställningar för nätverksgränssnitt](./media/create-user-defined-route/network-interface-settings.png)
        
    - Klicka på **aktiverad** för den **IP-vidarebefordring** inställningen genom att klicka på **spara**. IP-vidarebefordran måste vara aktiverat för varje nätverksgränssnitt som tar emot trafik som inte är adresserad till en IP-adress som tilldelats. Aktiverar IP-vidarebefordring inaktiverar Azures källan/målet kontroll för nätverksgränssnittet.
    - Klicka på **ipconfig1** i listan över IP-konfigurationer.
    - Klicka på **Statiska** för **tilldelning** för privata IP-adressen under **ipconfig1**, enligt följande bild:

        ![IP-konfiguration](./media/create-user-defined-route/ip-configuration.png)
    - I föregående bild visas ange *10.0.2.4* under **IP-adress** i den **privata IP-adressinställningarna**. Tilldela en statisk IP-adress till nätverksgränssnittet säkerställer att adressen inte ändras för den virtuella datorn som nätverksgränssnittet är kopplad till. Adressen som angetts är inte kopplat till en annan resurs i DMZ undernät som nätverksgränssnittet tillhör. 
    - Om du vill spara konfigurationen klickar du på **spara** under **ipconfig1**. Stäng inte rutan ipconfig1 tills du får ett meddelande i portalen som nätverksgränssnittet har sparats.
 
8. Skapa två routningstabeller. Routningstabeller innehålla noll eller flera vägar:

    - Klicka på vänster sida av portalen **+ ny** > **nätverk** > **routningstabellen**.
    - Ange följande värden under **skapa en routingtabell**, och klicka sedan på **skapa**:

        |Inställning|Värde|
        |---|---|
        |Namn|myRouteTable-Public|
        |Prenumeration|Välj din prenumeration|
        |Resursgrupp|Välj **använda befintliga**och välj **myResourceGroup**|
        |Plats|Östra USA|
    
    - Slutföra de föregående stegen i steg 8 igen, men namnet routningstabellen *myRouteTable privata*.
9. Lägga till vägar till den *myRouteTable offentliga* routningstabellen och associera routningstabellen till den *offentliga* undernät:

    - På den **söka resurser** överst i portalen och ange *myRouteTable offentliga*. Klicka på **myRouteTable offentliga** när den visas i sökresultaten.
    - Under **myRouteTable offentliga**, klickar du på **vägar** i listan över **inställningar**.
    - Klicka på **+ Lägg till** under **myRouteTable Public - vägar**.
    -  Standard Azure vägar trafik mellan alla undernät i ett virtuellt nätverk. Skapa en väg om du vill ändra Azures standard routning så som trafik från den *offentliga* undernät dirigeras via en NVA i stället för direkt till den *privata* undernät. Ange följande värden den **Lägg till väg** bladet som visas och klicka sedan på **OK**:

        |Inställning|Värde|Förklaring|
        |---|---|---|
        |Vägnamnet|ToPrivateSubnet|Den här vägen dirigerar trafik till det privata undernätet via virtuell nätverksenhet.|
        |Adressprefix|10.0.1.0/24| All trafik som skickas till alla adresser i det här adressprefixet (10.0.1.0 - 10.0.1.254) skickas till virtuell nätverksenhet.|
        |Nexthop-typ| Välj **virtuell installation**||
        |Nexthop-adress|10.0.2.4| Statisk privat IP-adressen för nätverksgränssnittet som är kopplad till virtuell nätverksenhet. Är den enda nexthop-typ som du kan ange en adress för **virtuell installation**.|

    - Under **myRouteTable offentliga**, klickar du på **undernät** under **inställningar**. 
    - Klicka på **+ associera** under **myRouteTable Public - undernät**.
    - Klicka på **för virtuella nätverk** under **Associera undernät**, klicka på **myVnet**.
    - Klicka på **undernät** under **Associera undernät**, klicka på **offentliga** under **välja undernätet**. 
    - Om du vill spara konfigurationen klickar du på **OK** under **Associera undernät**. Ett undernät kan ha noll eller en vägtabell associerad till den.
10. Fullständiga steg 9 igen, söker efter **myRouteTable privata**, skapar en väg med följande inställningar och sedan associera routningstabellen till den **privata** undernätet för den **myVnet** virtuellt nätverk:

    |Inställning|Värde|Förklaring|
    |---|---|---|
    |Vägnamnet|ToPublicSubnet|Den här vägen dirigerar trafik till det offentliga undernätet via virtuell nätverksenhet.|
    |Adressprefix|10.0.0.0/24| All trafik som skickas till alla adresser i det här adressprefixet (10.0.0.0 - 10.0.1.254) skickas till virtuell nätverksenhet.|
    |Nexthop-typ| Välj **virtuell installation**||
    |Nexthop-adress|10.0.2.4||

    Nätverkstrafiken mellan resurser i privata och offentliga undernät flödar genom virtuell nätverksenhet. 
11. **Valfritt:** skapa en virtuell dator i de offentliga och privata undernät och validera kommunikation mellan de virtuella datorerna dirigeras via virtuell nätverksenhet genom att slutföra stegen i [Validera routning](#validate-routing). 
12. **Valfria**: ta bort resurser som du skapar i den här självstudiekursen genom att slutföra stegen i [bort resurser](#delete-resources).

## <a name="validate-routing"></a>Validera Routning

1. Om du inte redan gjort slutföra stegen i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance).
2. Klicka på den **prova** i rutan nedan, vilket öppnar Shell för Azure-molnet. Om du uppmanas att logga in till Azure med hjälp av din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud-gränssnittet är ett kostnadsfritt bash-gränssnitt med Azure-kommandoradsgränssnittet förinstallerat. 

    Följande skript skapar två virtuella datorer i den *offentliga* undernät och en i den *privata* undernät. Skripten även aktivera IP-vidarebefordring för nätverksgränssnittet i operativsystemet på en NVA att aktivera operativsystemet att dirigera trafik via nätverksgränssnittet. Produktion NVA kontrollerar vanligtvis trafiken innan vidarebefordring, men i den här självstudiekursen enkla NVA dirigerar bara trafik utan att kontrollera den. 

    Klicka på den **kopiera** knappen i den **Linux** eller **Windows** skript som följer och klistra in skriptinnehållet i i en textredigerare. Ändra lösenordet för den *adminPassword* variabel och sedan klistra in skriptet i Azure Cloud-gränssnittet. Kör skriptet för operativsystemet som du valde när du skapade virtuell nätverksenhet i steg 6 i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Validera kommunikation mellan de virtuella datorerna i de offentliga och privata undernät. 

    - Öppna en [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) eller [fjärrskrivbord](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows)-anslutning till den offentliga IP-adressen för den *myVm offentliga* virtuella datorn.
    - Från en kommandotolk på den *myVm offentliga* virtuell dator, ange `ping myVm-Private`. Du får svar eftersom en NVA dirigerar trafik från allmänheten privata undernät.
    - Från den *myVm offentliga* virtuell dator som kör en spårningsrutt mellan de virtuella datorerna i de offentliga och privata undernät. Ange rätt kommando som följer, beroende på vilket operativsystem du har installerat i de virtuella datorerna i de offentliga och privata undernät:
        - **Windows**: Kör från en kommandotolk i `tracert myvm-private` kommando.
        - **Ubuntu**: kör den `tracepath myvm-private` kommando.
      Trafik som passerar genom 10.0.2.4 (NVA) innan det nådde 10.0.1.4 (virtuell dator i privata undernät). 
    - Slutföra de föregående stegen genom att ansluta till den *myVm privata* virtuella datorn och pinga den *myVm offentliga* virtuella datorn. Spåra väg visar kommunikation reser via 10.0.2.4 innan det nådde 10.0.0.4 (virtuell dator i offentliga undernät).

      > [!NOTE]
      > De här stegen kan du bekräfta routning mellan Azure privata IP-adresser. Om du vill vidarebefordra eller proxy trafik till offentliga IP-adresser via en virtuell nätverksenhet:
      > - Anordningen måste ange nätverksadresser eller proxy-funktioner. Om nätverksadresser, anordningen måste översätta källan IP-adress till sin egen och vidarebefordrar begäran till den offentliga IP-adressen. Om anordningen har nätverksadress översättas källadress eller är proxy, Azure översätter det virtuell nätverksenhets privata IP-adressen till en offentlig IP-adress. Mer information om de olika metoderna Azure använder för att översätta privata IP-adresser till offentliga IP-adresser finns [förstå utgående anslutningar](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
      > - En extra väg i routningstabellen som prefix: 0.0.0.0/0, nästa hopptyp VirtualAppliance och nästa hopp-IP-adress 10.0.2.4 (i föregående exempelskriptet).
      >
    - **Du kan också**: Använd för att validera nästa hopp mellan två virtuella datorer i Azure, Azure Nätverksbevakaren nästa hopp-kapacitet. Innan du använder Nätverksbevakaren, måste du först [skapa en instans av Azure Nätverksbevakaren](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för den region som du vill använda den i. I den här kursen används region oss Öst. När du har aktiverat en Nätverksbevakaren-instans för regionen, ange följande kommando för att se nästa hopp-information mellan de virtuella datorerna i de offentliga och privata undernät:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       Returnerar resultatet *10.0.2.4* som den **nextHopIpAddress** och *VirtualAppliance* som den **nextHopType**. 

> [!NOTE]
> För att illustrera begreppen i den här självstudiekursen offentliga IP-adresser tilldelas virtuella datorer i offentligt och privata undernät och alla nätverksåtkomst port är aktiverat i Azure för både virtuella datorer. När du skapar virtuella datorer för produktion, du kan inte tilldela offentliga IP-adresser till dem och filtrera nätverkstrafiken till undernätet för privata genom att distribuera en virtuell nätverksenhet framför det. eller genom att tilldela en nätverkssäkerhetsgrupp i undernät nätverksgränssnitt, eller båda. Läs mer om nätverkssäkerhetsgrupper i [Nätverkssäkerhetsgrupper](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Den här kursen kräver ett befintligt virtuellt nätverk med två undernät. Klicka på den **prova** i rutan nedan för att snabbt skapa ett virtuellt nätverk. Klicka på den **prova** knappen öppnas den [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Även om molnet Shell Kör PowerShell eller ett Bash-gränssnitt i det här avsnittet, används Bash-gränssnitt för att skapa virtuella nätverk. Bash-gränssnitt har Azure-kommandoradsgränssnittet installerad. Om du uppmanas av molnet gränssnittet loggar du in på Azure med din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p). För att skapa det virtuella nätverket används i den här kursen, klickar du på den **kopiera** knappen i följande ruta och sedan klistra in skriptet i Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Läs mer om hur du använder portalen, PowerShell eller en Azure Resource Manager-mall för att skapa ett virtuellt nätverk i [skapa ett virtuellt nätverk](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Ta bort resurser

När du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen. Företagsportalen är öppet gör du följande:

1. Skriv i sökrutan portal **myResourceGroup**. I sökresultaten klickar du på **myResourceGroup**.
2. På den **myResourceGroup** bladet, klickar du på den **ta bort** ikon.
3. Bekräfta borttagningen, i den **typ av RESURSGRUPPENS namn** ange **myResourceGroup**, och klicka sedan på **ta bort**.

## <a name="next-steps"></a>Nästa steg

- Skapa en [högtillgänglig virtuell nätverksenhet](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverksenheter har ofta flera nätverksgränssnitt och IP-adresser som tilldelats. Lär dig hur du [lägga till nätverksgränssnitt i en befintlig virtuell dator](virtual-network-network-interface-vm.md#vm-add-nic) och [lägga till IP-adresser till en befintlig nätverksgränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses). Även om alla storlekar för virtuella datorer kan ha minst två nätverkskort som är kopplade till dem, stöder storlek på varje virtuell dator maximalt antal nätverksgränssnitt. Information om hur många nätverkskort varje virtuell dator har stöd för storleken finns [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) storlekar för virtuella datorer. 
- Skapa en användardefinierad väg för att tvinga tunnel trafik lokalt via en [plats-till-plats VPN-anslutning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
