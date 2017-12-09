---
title: "Skapa en Internetriktade belastningsutjämnare med IPv6 - Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare med IPv6 i Azure Resource Manager med hjälp av Azure CLI"
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
ms.openlocfilehash: 3ae62ddd350204d801012b9810aec669abe55817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Skapa en Internetuppkopplad belastningsutjämnare med IPv6 i Azure Resource Manager med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Mall](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En Azure belastningsutjämnare är en Layer 4-belastningsutjämnare (TCP, UDP). Belastningsutjämnaren ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänstinstanser i molntjänster eller virtuella datorer i en belastningsutjämningsuppsättning. Azure belastningsutjämnare kan även presentera dessa tjänster på flera portar, flera IP-adresser eller både och.

## <a name="example-deployment-scenario"></a>Exempelscenario för distribution

Följande diagram illustrerar nätverkslösning för belastningsutjämning som distribueras med hjälp av mallen exempel som beskrivs i den här artikeln.

![Belastningsutjämningsscenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

I det här scenariot skapar du följande Azure-resurser:

* två virtuella datorer (VM)
* ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4 och IPv6-adresser som tilldelats
* en Internetriktade belastningsutjämnare med en IPv4 och IPv6-offentlig IP-adress
* en Tillgänglighetsuppsättning som innehåller två virtuella datorer
* två läsa in belastningsutjämning regler för att mappa offentliga VIP till privata slutpunkter

## <a name="deploying-the-solution-using-the-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI

Följande steg beskriver hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager med CLI. Med Azure Resource Manager skapas och konfigureras varje resurs separat, och läggs sedan ihop för att skapa en resurs.

För att distribuera en belastningsutjämnare måste du skapa och konfigurera följande objekt:

* IP-konfiguration på klientsidan – innehåller offentliga IP-adresser för inkommande nätverkstrafik.
* Backend-adresspool (serverdelspool) – innehåller nätverksgränssnitten (NIC) som de virtuella datorerna använder för att ta emot nätverkstrafik från belastningsutjämnaren.
* Belastningsutjämningsregler – innehåller regler för mappning av en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen.
* NAT-regler för inkommande trafik – innehåller regler för mappning av en offentlig port i belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspoolen.
* Avsökningar – innehåller hälsoavsökningar som används för att kontrollera tillgängligheten av instanser av virtuella datorer i backend-adresspoolen.

Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Ställ in CLI miljön att använda Azure Resource Manager

I det här exemplet kör vi CLI-verktygen i PowerShell-Kommandotolken. Vi använder inte Azure PowerShell-cmdlets, men vi använder PowerShells skriptfunktioner för att förbättra läsbarhet och återanvändning.

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör den **azure config mode** kommando för att växla till Resource Manager-läget.

    ```azurecli
    azure config mode arm
    ```

    Förväntad utdata:

        info:    New mode is arm

3. Logga in på Azure och få en lista över prenumerationer.

    ```azurecli
    azure login
    ```

    Ange dina autentiseringsuppgifter för Azure när du tillfrågas.

    ```azurecli
    azure account list
    ```

    Välj den prenumeration som du vill använda. Anteckna prenumerations-Id för nästa steg.

4. Ställ in PowerShell variabler för användning med CLI-kommandona.

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

1. Skapa en resursgrupp

    ```azurecli
    azure group create $rgName $location
    ```

2. Skapa en belastningsutjämnare

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Skapa ett virtuellt nätverk (VNet).

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

    Skapa två undernät i detta virtuella nätverk.

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Skapa den offentliga IP-adresser för frontend-pool

1. Ställ in variablerna PowerShell

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Skapa en offentlig IP-adress frontend IP-adresspool.

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > Belastningsutjämnaren använder domänetiketten för den offentliga IP-adressen som sitt fullständiga domännamn (FQDN). Detta en ändring från klassisk distribution som använder Molntjänsten namn som belastningsutjämnare FQDN.
    > I det här exemplet FQDN är *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Skapa frontend och backend-pooler

Det här exemplet skapar frontend IP-adresspool som tar emot inkommande nätverkstrafik på belastningsutjämnaren och backend-IP-pool där frontend poolen skickar belastningsutjämnade nätverkstrafik.

1. Ställ in variablerna PowerShell

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Skapa en IP-adresspool för klientsidan och koppla den offentliga IP-adressen som du skapade i föregående steg och belastningsutjämnaren.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Skapa avsökningen, NAT-regler och LB regler

I det här exemplet skapas följande objekt:

* en avsökning-regel för att söka efter anslutning till TCP-port 80
* en NAT-regel för att översätta all inkommande trafik på port 3389 till port 3389 för RDP<sup>1</sup>
* en NAT-regel för att översätta all inkommande trafik på port 3391 till port 3389 för RDP<sup>1</sup>
* En belastningsutjämningsregel som balanserar all inkommande trafik på port 80 till port 80 för adresserna i backend-poolen.

<sup>1</sup> NAT-regler associeras med en specifik instans av virtuella datorer bakom belastningsutjämnaren. Den nätverkstrafik som ankommer till port 3389 skickas till specifik virtuell dator och port som associeras med NAT-regeln. Du måste ange ett protokoll (UDP eller TCP) för en NAT-regel. Båda protokollen kan inte tilldelas till samma port.

1. Ställ in variablerna PowerShell

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Skapa avsökningen

    I följande exempel skapas en TCP-avsökning som kontrollerar anslutningen till backend-TCP-port 80 var 15: e sekund. Den markeras backend-resursen tillgänglig efter två på varandra följande fel.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Skapa inkommande NAT-regler som tillåter RDP-anslutningar till backend-resurser

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Skapa regler som skickar trafik till olika portar beroende på vilken frontend tog emot begäran för en belastningsutjämnare

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Kontrollera inställningarna

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

Skapa nätverkskort och kopplar dem till NAT-regler, regler för inläsning av belastningsutjämnare och avsökningar.

1. Ställ in variablerna PowerShell

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

2. Skapa ett nätverkskort för varje serverdel och Lägg till en IPv6-konfiguration.

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Skapa backend-VM resurser och koppla varje nätverkskort

Du måste ha ett lagringskonto för att skapa virtuella datorer. Belastningsutjämning måste de virtuella datorerna ska vara medlemmar i en tillgänglighetsuppsättning. Mer information om hur du skapar virtuella datorer finns [skapa en virtuell Azure-dator med hjälp av PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)

1. Ställ in variablerna PowerShell

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
    > Det här exemplet använder användarnamn och lösenord för de virtuella datorerna i klartext. Lämplig måste vara försiktig när du använder autentiseringsuppgifter i klartext. Ett säkrare sätt hantera autentiseringsuppgifter i PowerShell finns i [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

2. Skapa storage-konto och tillgänglighet?

    Du kan använda ett befintligt lagringskonto när du skapar de virtuella datorerna. Följande kommando skapar ett nytt lagringskonto.

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

    Skapa sedan tillgänglighetsuppsättningen.

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Skapa virtuella datorer med associerade nätverkskort

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
