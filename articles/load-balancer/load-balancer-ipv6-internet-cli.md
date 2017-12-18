---
title: "Skapa en offentlig belastningsutjämnare med IPv6 - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en offentlig belastningsutjämnare med IPv6 i Azure Resource Manager med hjälp av Azure CLI."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: "IPv6, azure belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot"
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 3abd47460999f7b059469a58a59a3e297e88effb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Skapa en offentlig belastningsutjämnare med IPv6 i Azure Resource Manager med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En Azure belastningsutjämnare är en Layer 4-belastningsutjämnare (TCP, UDP). Belastningsutjämnare ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfri tjänstinstanser i molntjänster eller virtuella datorer i en belastningen belastningsutjämnaren. Belastningsutjämnare kan också visa dessa tjänster på flera portar eller flera IP-adresser eller båda.

## <a name="example-deployment-scenario"></a>Exempelscenario för distribution

Följande diagram illustrerar belastningsutjämning lösning som distribueras med hjälp av mallen exempel som beskrivs i den här artikeln.

![Belastningsutjämningsscenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

I det här scenariot skapar du följande Azure-resurser:

* två virtuella datorer (VM)
* ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4 och IPv6-adresser som tilldelats
* En offentlig belastningsutjämnare med en IPv4- och en offentlig IP-adress för IPv6
* En tillgänglighetsuppsättning med två virtuella datorer
* två läsa in belastningsutjämning regler för att mappa offentliga VIP till privata slutpunkter

## <a name="deploy-the-solution-by-using-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI

Följande steg visar hur du skapar en offentlig belastningsutjämnare med Azure Resource Manager med Azure CLI. Med Azure Resource Manager kan du skapa och konfigurera varje objekt, och placera dem tillsammans för att skapa en resurs.

Om du vill distribuera en belastningsutjämnare, skapa och konfigurera följande objekt:

* **Frontend IP-konfiguration**: innehåller den offentliga IP-adresser för inkommande trafik.
* **Backend-adresspool**: innehåller nätverksgränssnitt (NIC) för de virtuella datorerna att ta emot trafik från belastningsutjämnaren.
* **Belastningsutjämningsreglerna**: innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port i backend-adresspool.
* **Inkommande NAT-regler**: innehåller network adress translation (NAT) regler som mappar en offentlig port på belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspoolen.
* **Avsökningar**: innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för en virtuell datorinstans i backend-adresspoolen.

Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](load-balancer-arm.md).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Konfigurera Azure CLI-miljön att använda Azure Resource Manager

I det här exemplet kör du Azure CLI-verktygen i ett PowerShell-kommandofönster. För att förbättra läsbarhet och återanvändning kan använda du PowerShells skriptfunktioner, inte Azure PowerShell-cmdlets.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.

2. Om du vill växla till läget Resource Manager kör den **azure config mode** kommando:

    ```azurecli
    azure config mode arm
    ```

    Förväntad utdata:

        info:    New mode is arm

3. Logga in på Azure och få en lista över prenumerationer:

    ```azurecli
    azure login
    ```

4. Ange dina autentiseringsuppgifter för Azure i Kommandotolken:

    ```azurecli
    azure account list
    ```

5. Välj den prenumeration som du vill använda och notera prenumerations-ID för användning i nästa steg.

6. Ställ in PowerShell variabler för användning med Azure CLI-kommandon:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Skapa en resursgrupp, en belastningsutjämning, ett virtuellt nätverk och undernät

1. Skapa en resursgrupp:

    ```azurecli
    azure group create $rgName $location
    ```

2. Skapa en belastningsutjämnare:

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Skapa ett virtuellt nätverk:

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Skapa två undernät i det här virtuella nätverket:

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Skapa den offentliga IP-adresser för frontend-pool

1. Ställ in variablerna PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Skapa en offentlig IP-adress för frontend IP-adresspool:

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Belastningsutjämnaren använder domän etiketten för offentliga IP-Adressen som fullständigt kvalificerade domännamn (FQDN). Detta en ändring från klassisk distribution som använder Molntjänsten namn som belastningsutjämnare FQDN.
    >
    > I det här exemplet FQDN är *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Skapa frontend och backend-pooler

I det här avsnittet skapar du följande IP-adresspooler:
* Frontend IP-poolen som tar emot inkommande nätverkstrafik på belastningsutjämnaren.
* Backend-IP-pool där frontend poolen skickar nätverkstrafik Utjämning av nätverksbelastning.

1. Ställ in variablerna PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Skapa en frontend IP-adresspool och koppla den till den offentliga IP-Adressen som du skapade i föregående steg och belastningsutjämnaren.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Skapa avsökningen, NAT-regler och belastningsutjämningsreglerna

I det här exemplet skapas följande objekt:

* Avsökningen regel att söka efter anslutning till TCP-port 80.
* En NAT-regel för att översätta all inkommande trafik på port 3389 till port 3389 för RDP.\*
* En NAT-regel för att översätta all inkommande trafik på port 3391 till port 3389 för remote desktop protocol (RDP).\*
* en regel för belastningsutjämnare ska hantera all inkommande trafik på port 80 till port 80 på adresser i backend-poolen.

\*NAT-regler som är associerade med en specifik virtuell dator instans bakom belastningsutjämnaren. Den nätverkstrafik som anländer på port 3389 skickas till en specifik virtuell dator och port som är associerad med NAT-regeln. Du måste ange ett protokoll (UDP eller TCP) för en NAT-regel. Du kan inte tilldela båda protokollen till samma port.

1. Ställ in variablerna PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Skapa avsökningen.

    I följande exempel skapas en TCP-avsökning som kontrollerar anslutningen till backend-TCP-port 80 var 15: e sekund. Efter två på varandra följande fel markeras backend-resurs som inte tillgänglig.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Skapa inkommande NAT-regler som tillåter RDP-anslutningar till backend-resurser:

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Skapa belastningen belastningsutjämnaren regler som skickar trafik till andra backend-portar, beroende på klientdelen som tog emot begäran.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Kontrollera inställningarna:

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    Förväntad utdata:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Skapa nätverkskort

Skapa nätverkskort och koppla dem till NAT-regler, regler för inläsning av belastningsutjämnare och avsökningar.

1. Ställ in variablerna PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Skapa ett nätverkskort för varje serverdel och Lägg till en IPv6-konfiguration:

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Skapa backend-VM resurser och koppla varje nätverkskort

Du måste ha ett lagringskonto för att skapa virtuella datorer. Belastningsutjämning måste de virtuella datorerna ska vara medlemmar i en tillgänglighetsuppsättning. Mer information om hur du skapar virtuella datorer finns [skapa en virtuell dator i Azure med hjälp av PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Ställ in variablerna PowerShell:

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Det här exemplet använder användarnamn och lösenord för de virtuella datorerna i klartext. Vidta lämpliga försiktighetsåtgärder när du använder dessa autentiseringsuppgifter i klartext. Ett säkrare sätt hantera autentiseringsuppgifter i PowerShell finns i [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

2. Skapa uppsättningen storage-konto och tillgänglighet.

    Du kan använda ett befintligt lagringskonto när du skapar de virtuella datorerna. Du kan skapa ett nytt lagringskonto med hjälp av följande kommando:

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Skapa tillgänglighetsuppsättningen:

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Skapa de virtuella datorerna med associerade nätverkskort:

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-cli.md)  
[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)  
[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
