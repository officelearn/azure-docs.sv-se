---
title: "Skapa en Internetaktiverad belastningsutjämnare – Azure CLI | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i Resource Manager med hjälp av Azure CLI"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ba36b7f6d2ae3cc4d63829ffb757ff7b311e467b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="creating-an-internet-load-balancer-using-the-azure-cli"></a>Skapa en intern belastningsutjämnare med hjälp av Azure CLI

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Mall](../load-balancer/load-balancer-get-started-internet-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också läsa om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av en klassisk distribution](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI

Följande steg beskriver hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager med CLI. Med Azure Resource Manager skapas och konfigureras varje resurs separat, och läggs sedan ihop för att skapa en resurs.

Du måste skapa och konfigurera följande objekt för att distribuera en belastningsutjämnare:

* IP-konfiguration på klientsidan – innehåller offentliga IP-adresser för inkommande nätverkstrafik.
* Backend-adresspool (serverdelspool) – innehåller nätverksgränssnitten (NIC) som de virtuella datorerna använder för att ta emot nätverkstrafik från belastningsutjämnaren.
* Belastningsutjämningsregler – innehåller regler för mappning av en offentlig port på belastningsutjämnaren till en port i backend-adresspoolen.
* NAT-regler för inkommande trafik – innehåller regler för mappning av en offentlig port i belastningsutjämnaren till en port för en specifik virtuell dator i backend-adresspoolen.
* Avsökningar – innehåller hälsoavsökningar som används för att kontrollera tillgängligheten av instanser av virtuella datorer i backend-adresspoolen.

Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Konfigurera CLI för att använda Resource Manager

1. Om du aldrig har använt Azure CLI, se [installera och konfigurera Azure CLI](../cli-install-nodejs.md) och följ instruktionerna upp till den punkt där du väljer Azure-konto och prenumeration.
2. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, som det visas nedan.

    ```azurecli
        azure config mode arm
    ```

    Förväntad utdata:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Skapa ett virtuellt nätverk och en offentlig IP-adress för IP-adresspoolen på klientsidan

1. Skapa ett virtuellt nätverk (VNet) med namnet *NRPVnet* på platsen East USA med hjälp av en resursgrupp med namnet *NRPRG*.

    ```azurecli
        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16
    ```

    Skapa ett undernät med namnet *NRPVnetSubnet* med CIDR-blocket 10.0.0.0/24 i *NRPVnet*.

    ```azurecli
        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24
    ```

2. Skapa en offentlig IP-adress med namnet *NRPPublicIP* som ska användas av en IP-adresspool på klientsidan med DNS-namnet *loadbalancernrp.eastus.cloudapp.azure.com*. I kommandot nedan används den statiska allokeringstypen och en timeout för inaktivitet på 4 minuter.

    ```azurecli
        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4
    ```

   > [!IMPORTANT]
   > Belastningsutjämnaren använder domänetiketten för den offentliga IP-adressen som sitt fullständiga domännamn (FQDN). Det här är en ändring från den klassiska distributionen, som använder molntjänsten som belastningsutjämnarens fullständiga domännamn.
   > I det här exemplet är det fullständiga domännamnet *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare

Följande kommando skapar en belastningsutjämnare med namnet *NRPlb* i *NRPRG*-resursgruppen på Azure-platsen *East USA*.

    ```azurecli
    azure network lb create NRPRG NRPlb eastus
    ```

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Skapa en IP-adresspool på klientsidan och en backend-adresspool
Det här exemplet visar hur du skapar IP-adresspoolen på klientsidan som tar emot den inkommande nätverkstrafiken på belastningsutjämnaren och backend-IP-adresspoolen där serverdelspoolen skickar den belastningsutjämnade nätverkstrafiken.

1. Skapa en IP-adresspool för klientsidan och koppla den offentliga IP-adressen som du skapade i föregående steg och belastningsutjämnaren.

    ```azurecli
        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip
    ```

2. Konfigurera en adresspool på serversidan som används för att ta emot inkommande trafik från IP-poolen på klientsidan.

    ```azurecli
        azure network lb address-pool create NRPRG NRPlb NRPbackendpool
    ```

## <a name="create-lb-rules-nat-rules-and-probe"></a>Skapa LB-regler, NAT-regler och avsökning

I det här exemplet skapas följande objekt:

* En NAT-regel som översätter all inkommande trafik på port 21 till port 22<sup>1</sup>
* En NAT-regel som översätter all inkommande trafik på port 23 till port 22
* En belastningsutjämningsregel som balanserar all inkommande trafik på port 80 till port 80 för adresserna i backend-poolen.
* En avsökningsregel som kontrollerar hälsostatusen på en sida med namnet *HealthProbe.aspx*.

<sup>1</sup> NAT-regler associeras med en specifik instans av virtuella datorer bakom belastningsutjämnaren. Nätverkstrafik som kommer till port 21 skickas till en specifik virtuell dator på port 22 som är associerad med den här NAT-regeln. Du måste ange ett protokoll (UDP eller TCP) för en NAT-regel. Båda protokollen kan inte tilldelas till samma port.

1. Skapa NAT-reglerna.

    ```azurecli
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22
    ```

2. Skapa en belastningsutjämningsregel.

    ```azurecli
        azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name NRPfrontendpool --backend-address-pool-name NRPbackendpool
    ```

3. Skapa en hälsoavsökning.

    ```azurecli
        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4
    ```

4. Kontrollera inställningarna.

    ```azurecli
        azure network lb show nrprg nrplb
    ```

    Förväntad utdata:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Skapa nätverkskort

Du måste skapa nätverkskort (eller ändra befintliga) och associera dem med NAT-regler, belastningsutjämningsregler och avsökningar.

1. Skapa ett nätverkskort med namnet *lb-nic1-be* och associera det med NAT-regeln *rdp1* och backend-adresspoolen *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
    ```

    Förväntad utdata:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Skapa ett nätverkskort med namnet *lb-nic2-be* och associera det med NAT-regeln *rdp2* och backend-adresspoolen *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
    ```

3. Skapa en virtuell dator (VM) med namnet *web1* och associera den med nätverkskortet med namnet *lb-nic1-be*. Ett lagringskonto med namnet *web1nrp* skapas innan du kör kommandot nedan.

    ```azurecli
        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

    > [!IMPORTANT]
    > Virtuella datorer i en belastningsutjämnare måste finnas i samma tillgänglighetsuppsättning. Använd `azure availset create` för att skapa en tillgänglighetsuppsättning.

    Resultatet bör likna följande:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    > [!NOTE]
    > Ett meddelande som anger att **ingen offentlig IP-adress har konfigurerats för nätverkskortet** visas eftersom nätverkskortet som skapas för belastningsutjämnaren som ansluter till Internet använder belastningsutjämnarens offentliga IP-adress.

    Eftersom nätverkskortet *lb-nic1-be* är associerat med NAT-regeln *rdp1* kan du ansluta till *web1* med hjälp av RDP via port 3441 på belastningsutjämnaren.

4. Skapa en virtuell dator (VM) med namnet *web2* och associera den med nätverkskortet med namnet *lb-nic2-be*. Ett lagringskonto med namnet *web1nrp* skapas innan du kör kommandot nedan.

    ```azurecli
        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="update-an-existing-load-balancer"></a>Uppdatera en befintlig belastningsutjämnare
Du kan lägga till regler som refererar till en befintlig belastningsutjämnare. I nästa exempel läggs en ny belastningsutjämningsregel till i en befintlig belastningsutjämnare, **NRPlb**

```azurecli
azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool
```

## <a name="delete-a-load-balancer"></a>Ta bort en belastningsutjämnare
Använd följande kommando om du vill ta bort en belastningsutjämnare:

```azurecli
azure network lb delete --resource-group nrprg --name nrplb
```

## <a name="next-steps"></a>Nästa steg
[Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-cli.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
