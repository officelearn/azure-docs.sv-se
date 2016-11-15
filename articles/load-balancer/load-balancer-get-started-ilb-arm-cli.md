---
title: "Skapa en intern belastningsutjämnare med hjälp av Azure CLI i Resource Manager | Microsoft Docs"
description: "Ta reda på hur du skapar en intern belastningsutjämnare med hjälp av Azure CLI i Resource Manager"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 16f4dcd7860bf2da1a15ce884fb86500a751e406


---
# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Skapa en intern belastningsutjämnare med hjälp av Azure CLI
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[klassisk distributionsmodell](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Distribuera lösningen med hjälp av Azure CLI
Följande steg beskriver hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager med CLI. Med Azure Resource Manager skapas och konfigureras varje resurs separat, och läggs sedan ihop för att skapa en resurs.

Du måste skapa och konfigurera följande objekt för att distribuera en belastningsutjämnare:

* **IP-konfiguration på klientsidan**: innehåller offentliga IP-adresser för inkommande nätverkstrafik
* **Serverdelsadresspool**: innehåller nätverksgränssnitten (nätverkskort) som gör det möjligt för de virtuella datorerna att ta emot nätverkstrafik från belastningsutjämnaren
* **Belastningsutjämningsregler**: innehåller regler som mappar en offentlig port på belastningsutjämnaren till en port i serverdelsadresspoolen
* **Ingående NAT-regler**: innehåller regler som mappar en offentlig port i belastningsutjämnaren till en port för en specifik virtuell dator i serverdelsadresspoolen
* **Avsökningar**: innehåller hälsoavsökningar som används för att kontrollera tillgängligheten för virtuella datorer i serverdelsadresspoolen

Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Konfigurera CLI för att använda Resource Manager
1. Om du aldrig har använt Azure CLI hittar du mer information i [Installera och konfigurera Azure CLI](../xplat-cli-install.md). Följ instruktionerna fram till den punkt där du kan välja Azure-konto och -prenumeration.
2. Kör kommandot **azure config mode** för att växla till Resource Manager-läge, enligt följande:
   
        azure config mode arm
   
    Förväntad utdata:
   
        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Stegvisa anvisningar för hur du skapar en intern belastningsutjämnare
1. Logga in i Azure.
   
        azure login
   
    Ange inte dina autentiseringsuppgifter för Azure när du uppmanas göra det.
2. Ändra kommandoverktygen till Azure Resource Manager-läge.
   
        azure config mode arm

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Alla resurser i Azure Resource Manager är kopplade till en resursgrupp. Om du inte redan gjort det skapar du en resursgrupp nu.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Skapa en intern belastningsutjämningsuppsättning
1. Skapa en intern belastningsutjämnare
   
    I följande scenario skapas en resursgrupp med namnet nrprg i regionen för östra USA.
   
        azure network lb create --name nrprg --location eastus
   
   > [!NOTE]
   > Alla resurser för en intern belastningsutjämnare, till exempel virtuella nätverk och undernät i virtuella nätverk, måste vara i samma resursgrupp och i samma region.
   > 
   > 
2. Skapa en IP-adress för klientdelen för den interna belastningsutjämnaren.
   
    IP-adressen som du använder måste vara inom undernätsintervallet för ditt virtuella nätverk.
   
        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
3. Skapa serverdelsadresspoolen.
   
        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
   
    När du har definierat en IP-adress för klientdelen och en serverdelsadresspool kan du skapa belastningsutjämningsregler, ingående NAT-regler och anpassade hälsoavsökningar.
4. Skapa en belastningsutjämningsregel för den interna belastningsutjämnaren.
   
    När du följer de här stegen skapar kommandot en belastningsutjämningsregel för avlyssning av port 1433 i klientdelspoolen och belastningsutjämnad nätverkstrafik skickas till serverdelsadresspoolen, som också använder port 1433.
   
        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
5. Skapa ingående NAT-regler.
   
    Ingående NAT-regler används för att skapa slutpunkter i en belastningsutjämnare som leder till en specifik virtuell datorinstans. I föregående steg skapade du två NAT-regler för fjärrskrivbord.
   
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389
   
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
6. Skapa hälsoavsökningar för belastningsutjämnaren.
   
    En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från belastningsutjämnaren tills den är tillbaka online och en avsökningskontroll visar att den är felfri.
   
        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
   
   > [!NOTE]
   > Microsoft Azure-plattformen använder en statisk, offentligt dirigerbar IPv4-adress för en rad olika administrativa scenarier. IP-adressen är 168.63.129.16. Den här IP-adressen får inte blockeras av eventuella brandväggar eftersom det kan orsaka oväntade resultat.
   > Vid belastningsutjämning i Azure används den här IP-adressen av övervakningsavsökningar från belastningsutjämnaren för att fastställa hälsotillståndet hos virtuella datorer i en belastningsutjämnad uppsättning. Om en nätverkssäkerhetsgrupp används för att begränsa trafik till virtuella datorer i Azure i en internt belastningsutjämnad uppsättning eller om den tillämpas på ett virtuellt nätverksundernät krävs en nätverkssäkerhetsregel som tillåter trafik från 168.63.129.16.
   > 
   > 

## <a name="create-nics"></a>Skapa nätverkskort
Du måste skapa nätverkskort (eller ändra befintliga) och associera dem med NAT-regler, belastningsutjämningsregler och avsökningar.

1. Skapa ett nätverkskort med namnet *lb-nic1-be* och associera till med NAT-regeln *rdp1* och serverdelsadresspoolen *beilb*.
   
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
   
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
2. Skapa ett nätverkskort med namnet *lb-nic2-be* och associera det till NAT-regeln *rdp2* och serverdelsadresspoolen *beilb*.
   
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
3. Skapa en virtuell dator med namnet *DB1* och associera den sedan till nätverkskortet *lb-nic1-be*. Ett lagringskonto med namnet *web1nrp* skapas innan du kör följande kommando:
   
        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
   
   > [!IMPORTANT]
   > Virtuella datorer i en belastningsutjämnare måste finnas i samma tillgänglighetsuppsättning. Använd `azure availset create` för att skapa en tillgänglighetsuppsättning.
   > 
   > 
4. Skapa en virtuell dator med namnet *DB2* och associera den till nätverkskortet med namnet *lb-nic2-be*. Ett lagringskonto med namnet *web1nrp* skapas innan följande kommando kördes.
   
        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Ta bort en belastningsutjämnare
Ta bort en belastningsutjämnare genom att använda följande kommando:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Nästa steg
[Konfigurera ett distributionsläge för en belastningsutjämnare med hjälp av käll-IP-tilldelning](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


