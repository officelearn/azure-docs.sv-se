---
title: "Skapa en användardefinierad väg väg trafik via en virtuell nätverksenhet - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en användardefinierad väg för att åsidosätta standardvärdet för Azures routning av Routning av nätverkstrafik via en virtuell nätverksenhet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: adfcf53f9fca0efafb538edfd65b95313dcf1559
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>Skapa en användardefinierad väg - Azure CLI

I den här kursen lär du dig hur du skapar användardefinierade vägar för att vidarebefordra trafik mellan två [virtuellt nätverk](virtual-networks-overview.md) undernät via en virtuell nätverksenhet. En virtuell nätverksenhet är en virtuell dator som kör ett nätverksprogram, till exempel en brandvägg. Mer information om förkonfigurerade virtuella nätverksenheter som du kan distribuera i Azure-nätverk finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

När du skapar undernät i ett virtuellt nätverk, Azure skapar standard [systemvägar](virtual-networks-udr-overview.md#system-routes) som gör att resurser i alla undernät för att kommunicera med varandra, enligt följande bild:

![Standardvägar](./media/create-user-defined-route/default-routes.png)

I de här självstudierna skapar du ett virtuellt nätverk med offentliga och privata DMZ undernät, som visas i bilden nedan. Vanligtvis webbservrar kan distribueras till en offentlig undernät och ett program eller en databasserver kan distribueras till ett privat undernät. Du skapar en virtuell dator ska fungera som en virtuell nätverksenhet i DMZ-undernät och du kan också skapa en virtuell dator i varje undernät som kommunicerar via nätverket virtuell installation. All trafik mellan de offentliga och privata undernäten dirigeras via installationen som visas i följande bild:

![Användardefinierade vägar](./media/create-user-defined-route/user-defined-routes.png)

Den här artikeln innehåller steg för att skapa en användardefinierad väg genom distributionsmodell hanteraren för filserverresurser, som är den distributionsmodell som vi rekommenderar att du använder när du skapar användardefinierade vägar. Om du behöver skapa en användardefinierad väg (klassisk), se [skapa en användardefinierad väg (klassisk)](virtual-network-create-udr-classic-cli.md). Om du inte är bekant med Azures distributionsmodeller [förstå Azure distributionsmodeller](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Mer information om användardefinierade vägar finns [användardefinierade vägar översikt](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Skapa vägar och virtuell nätverksenhet

Azure CLI-kommandona är desamma, om du kör kommandon från Windows, Linux eller macOS. Det finns dock scripting skillnader mellan gränssnitt för operativsystemet. Körning av skript i följande steg i en kräver installation och körning av Azure CLI-kommandona i ett Bash-gränssnitt. Du kan antingen [installera och konfigurera Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) på din dator eller klicka på den **prova** knappen i någon av att köra skript i Azure Cloud Shell-skript.
 
1. **Nödvändiga**: skapa ett virtuellt nätverk med två undernät genom att slutföra stegen i [skapa ett virtuellt nätverk](#create-a-virtual-network).
2. Om du kör Azure CLI från datorn, logga in på Azure med den `az login` kommando. Om du använder molntjänster Shell, loggas du automatiskt.
3. Ange några variabler som används i hela stegen:

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. Skapa en *DMZ* undernät i det virtuella nätverket som skapats i nödvändiga:

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. Skapa NVA virtuell dator. Tilldela statiska offentliga och privata IP-adresser till nätverksgränssnittet CLI skapas. Statiska adresser ändras inte för den virtuella datorn. En NVA kan vara en virtuell dator som kör Linux eller Windows-operativsystem. Kopiera skriptet för antingen operativsystemet för att skapa den virtuella datorn, och klistra in den i CLI. Skapa en virtuell dator i Windows, klistra in skriptet i en textredigerare, ändrar du värdet för den *AdminPassword* variabel och sedan klistra in den ändrade texten i CLI.

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. Aktivera IP-vidarebefordring för en NVA nätverksgränssnitt. Aktivera IP gör-vidarebefordring för ett nätverksgränssnitt Azure inte att kontrollera IP-adressen för källan/målet. Om du inte aktiverar den här inställningen kan trafik till en IP-adress än NIC som tar emot den, har släppts av Azure.

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. Skapa en routingtabell för den *offentliga* undernät.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. Standard Azure vägar trafik mellan alla undernät i ett virtuellt nätverk. Skapa en väg om du vill ändra Azures standard routning så att trafiken från offentliga undernät till undernätet för privata dirigeras via en NVA i stället för direkt till det privata undernätet.

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. Koppla den *myRouteTable offentliga* routningstabellen till den *offentliga* undernät. Associera en routingtabell till ett undernät gör att Azure för att dirigera all utgående trafik från undernätet enligt vägar i routningstabellen. En routingtabell kan vara kopplad till noll eller flera undernät, ett undernät kan ha noll eller en vägtabell associerad till den.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. Skapa routningstabellen för den *privata* undernät.

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. Skapa en väg att vidarebefordra trafik från den *privata* undernät så att den *offentliga* undernätet via NVA virtuella datorn.

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. Associera routningstabellen till den *privata* undernät.

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **Valfritt:** skapa en virtuell dator i de offentliga och privata undernät och validera kommunikation mellan de virtuella datorerna dirigeras via virtuell nätverksenhet genom att slutföra stegen i [Validera routning](#validate-routing).
14. **Valfria**: Om du vill ta bort resurser som du skapar i den här självstudiekursen måste du slutföra stegen i [bort resurser](#delete-resources).

## <a name="validate-routing"></a>Validera Routning

1. Om du inte redan gjort slutföra stegen i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance).
2. Klicka på den **prova** i rutan nedan, vilket öppnar Shell för Azure-molnet. Om du uppmanas att logga in till Azure med hjälp av din [Azure-konto](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/offers/ms-azr-0044p). Azure Cloud-gränssnittet är ett kostnadsfritt bash-gränssnitt med Azure-kommandoradsgränssnittet förinstallerat. 

    Följande skript skapar två virtuella datorer i den *offentliga* undernät och en i den *privata* undernät. Skripten även aktivera IP-vidarebefordring för nätverksgränssnittet i operativsystemet på en NVA att aktivera operativsystemet att dirigera trafik via nätverksgränssnittet. Produktion NVA kontrollerar vanligtvis trafiken innan vidarebefordring, men i den här självstudiekursen enkla NVA dirigerar bara trafik utan att kontrollera den. 

    Klicka på den **kopiera** knappen i den **Linux** eller **Windows** skript som följer och klistra in skriptinnehållet i i en textredigerare. Ändra lösenordet för den *adminPassword* variabel och sedan klistra in skriptet i Azure Cloud-gränssnittet. Kör skriptet för operativsystemet som du valde när du skapade virtuell nätverksenhet i steg 5 i [skapa vägar och virtuell nätverksenhet](#create-routes-and-network-virtual-appliance). 

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
    - **Du kan också**: använda nästa hopp-funktionen för Azure Nätverksbevakaren för att validera nästa hopp mellan två virtuella datorer i Azure. Innan du använder Nätverksbevakaren, måste du först [skapa en instans av Azure Nätverksbevakaren](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för den region som du vill använda den i. I den här kursen används region oss Öst. När du har aktiverat en Nätverksbevakaren-instans för regionen, ange följande kommando för att se nästa hopp-information mellan de virtuella datorerna i de offentliga och privata undernät:
     
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

När du är klar med den här kursen kan du vill ta bort de resurser som du har skapat, så att du inte betalar användningsavgifter. En resursgrupp också tar du bort alla resurser som finns i resursgruppen. Ange följande kommando i en CLI-session:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Nästa steg

- Skapa en [högtillgänglig virtuell nätverksenhet](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuella nätverksenheter har ofta flera nätverksgränssnitt och IP-adresser som tilldelats. Lär dig hur du [lägga till nätverksgränssnitt i en befintlig virtuell dator](virtual-network-network-interface-vm.md#vm-add-nic) och [lägga till IP-adresser till en befintlig nätverksgränssnittet](virtual-network-network-interface-addresses.md#add-ip-addresses). Även om alla storlekar för virtuella datorer kan ha minst två nätverkskort som är kopplade till dem, stöder storlek på varje virtuell dator maximalt antal nätverksgränssnitt. Information om hur många nätverkskort varje virtuell dator har stöd för storleken finns [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) och [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) storlekar för virtuella datorer. 
- Skapa en användardefinierad väg för att tvinga tunnel trafik lokalt via en [plats-till-plats VPN-anslutning](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
