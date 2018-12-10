---
title: Skapa en offentlig belastningsutjämnare med IPv6 - Azure CLI
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en offentlig belastningsutjämnare med IPv6 med Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, azure-belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: kumud
ms.openlocfilehash: 1caa8e7554024c3b2e3d86436d3d494d7995169a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142027"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Skapa en offentlig belastningsutjämnare med IPv6 med Azure CLI


En Azure Load Balancer är en Layer 4-lastbalanserare (TCP, UDP). Belastningsutjämnare ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfria tjänsteinstanser i cloud services och virtuella datorer i en belastningsutjämningsuppsättning. Belastningsutjämnare kan även presentera dessa tjänster på flera portar eller flera IP-adresser eller båda.

## <a name="example-deployment-scenario"></a>Exempelscenario för distribution

Följande diagram illustrerar belastningsutjämningslösning som distribueras med hjälp av mallen för exemplet som beskrivs i den här artikeln.

![Lastbalanseringsscenario](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

I det här scenariot skapar du följande Azure-resurser:

* Två virtuella datorer (VM)
* Ett virtuellt nätverksgränssnitt för varje virtuell dator med både IPv4 och IPv6-adresserna som tilldelats
* En offentlig belastningsutjämnare med en IPv4 och en offentlig IP-adress för IPv6
* En tillgänglighetsuppsättning som innehåller två virtuella datorer
* Två belastningsutjämningsregler för att mappa de offentliga virtuella IP-adresserna till de privata slutpunkterna

## <a name="deploy-the-solution-by-using-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI

Följande steg visar hur du skapar en offentlig belastningsutjämnare med hjälp av Azure CLI. Med CLI kan du skapa och konfigurera varje objekt separat, och placera dem tillsammans för att skapa en resurs.

Distribuera en belastningsutjämnare, skapa och konfigurera följande objekt:

* **Frontend-IP-konfigurationen**: innehåller offentliga IP-adresser för inkommande nätverkstrafik.
* **Backend adresspoolen**: innehåller nätverksgränssnitten (NIC) som de virtuella datorerna att ta emot nätverkstrafik från belastningsutjämnaren.
* **Regler för belastningsutjämning**: innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen.
* **Ingående NAT-regler**: innehåller regler network address translation (NAT) som mappar en offentlig port på belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspoolen.
* **Avsökningar**: innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för instanser av virtuella datorer i backend-adresspoolen.

## <a name="set-up-azure-cli"></a>Konfigurera Azure CLI

I det här exemplet kan köra du Azure CLI-verktygen i ett PowerShell-kommandofönster. För att förbättra läsbarheten och återanvändning kan använda du PowerShell-skript funktioner, inte Azure PowerShell-cmdlets.

1. [Installera och konfigurera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) genom att följa stegen i den länkade artikeln och logga in på kontot.

2. Konfigurera PowerShell variabler för användning med Azure CLI-kommandon:

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

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Skapa en resursgrupp, en belastningsutjämnare, ett virtuellt nätverk och undernät

1. Skapa en resursgrupp:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Skapa en belastningsutjämnare:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Skapa ett virtuellt nätverk:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Skapa två undernät i det här virtuella nätverket:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Skapa den offentliga IP-adresser för adresspoolen på klientsidan

1. Ställ in variabler för PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Skapa en offentlig IP-adress för IP-adresspoolen på klientsidan:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > Belastningsutjämnaren använder domänetiketten för den offentliga IP-Adressen som dess fullständiga domännamn (FQDN). En ändring från den klassiska distributionen, som använder Molntjänsten namnet som belastningsutjämnarens fullständiga domännamn.
    >
    > I det här exemplet är det fullständiga Domännamnet *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Skapa frontend och backend-adresspooler

I det här avsnittet skapar du följande IP-adresspooler:
* IP-adresspoolen på klientsidan som tar emot den inkommande nätverkstrafiken på belastningsutjämnaren.
* Backend-IP-adresspoolen där serverdelspoolen skickar den belastningsutjämnade nätverkstrafiken.

1. Ställ in variabler för PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Skapa en IP-adresspool på klientsidan och koppla den till den offentliga IP-Adressen som du skapade i föregående steg och belastningsutjämnaren.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Skapa avsökning, NAT-regler och regler för belastningsutjämnare

I det här exemplet skapas följande objekt:

* En avsökningsregel som kontrollerar anslutning till TCP-port 80.
* En NAT-regel som översätter all inkommande trafik på port 3389 till port 3389 för RDP.\*
* En NAT-regel som översätter all inkommande trafik på port 3391 till port 3389 för remote desktop protocol (RDP).\*
* En belastningsutjämningsregel som balanserar all inkommande trafik på port 80 till port 80 för adresserna i backend poolen.

\* NAT-regler associeras med en specifik instans för virtuella datorer bakom belastningsutjämnaren. Den nätverkstrafik som kommer på port 3389 skickas till den specifika virtuella datorn och port som är associerat med NAT-regel. Du måste ange ett protokoll (UDP eller TCP) för en NAT-regel. Du kan inte tilldela båda protokollen på samma port.

1. Ställ in variabler för PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Skapa avsökningen.

    I följande exempel skapas en TCP-avsökning som kontrollerar anslutning till backend-TCP-port 80 var 15: e sekund. Efter två på varandra följande fel markeras backend-resursen som otillgängligt.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Skapa inkommande NAT-regler som tillåter RDP-anslutningar till backend-resurser:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Skapa regler för belastningsutjämnaren som skickar trafik till andra backend-portar, beroende på klientdelen som tog emot begäran.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Kontrollera inställningarna:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
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

Skapa nätverkskort och koppla dem till NAT-regler, belastningsutjämningsregler och avsökningar.

1. Ställ in variabler för PowerShell:

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
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Skapa backend-VM-resurser och bifoga varje nätverkskort

Om du vill skapa virtuella datorer, måste du ha ett lagringskonto. Belastningsutjämning måste de virtuella datorerna ska vara medlemmar i en tillgänglighetsuppsättning. Mer information om hur du skapar virtuella datorer finns i [skapa en Azure-dator med hjälp av PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Ställ in variabler för PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Det här exemplet använder användarnamn och lösenord för de virtuella datorerna i klartext. Vidta lämpliga försiktighetsåtgärder när du använder dessa autentiseringsuppgifter i klartext. En mer säker metod för hantering av autentiseringsuppgifter i PowerShell, finns det [ `Get-Credential` ](https://technet.microsoft.com/library/hh849815.aspx) cmdlet.

2. Skapa tillgänglighetsuppsättningen:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Skapa de virtuella datorerna med associerade nätverkskort:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>Nästa steg

[Komma igång med att konfigurera en intern lastbalanserare](load-balancer-get-started-ilb-arm-cli.md)  
[Konfigurera ett distributionsläge för lastbalanserare](load-balancer-distribution-mode.md)  
[Konfigurera timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md)
