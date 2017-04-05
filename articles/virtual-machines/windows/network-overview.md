---
title: "Virtuella nätverk och virtuella Windows-datorer i Azure | Microsoft Docs"
description: "Läs om nätverk i relation till grunderna för att skapa virtuella Windows-datorer i Azure."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5493e9f7-7d45-4e98-be9a-657a53708746
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b3a69fdbdab2d7118135ed3e9d26aa01adb52e17
ms.lasthandoff: 03/31/2017


---

# <a name="virtual-networks-and-windows-virtual-machines-in-azure"></a>Virtuella nätverk och virtuella Windows-datorer i Azure 

När du skapar en virtuell Azure-dator (VM) måste du skapa ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md) (VNet) eller använda ett befintligt VNet. Du måste också bestämma hur dina virtuella datorer är avsedda att användas på VNet. Det är viktigt att [planera innan du skapar resurser](../../virtual-network/virtual-network-vnet-plan-design-arm.md) och att säkerställa att du förstår [begränsningarna för nätverksresurser](../../azure-subscription-service-limits.md#networking-limits).

I följande bild visas virtuella datorer som webb- och databasservrar. Varje uppsättning virtuella datorer är tilldelade separata undernät i VNet.

![Azure-virtuellt nätverk](./media/network-overview/vnetoverview.png)

Du kan antingen skapa ett VNet innan du skapar en virtuell dator eller skapa VNet när du skapar en virtuell dator. Du måste antingen skapa ett VNet själv eller så skapas ett för dig när du skapar en virtuell dator.

Du skapar dessa resurser för att stödja kommunikation med en virtuell dator:

- Nätverksgränssnitt
- IP-adresser
- Virtuellt nätverk och undernät

Utöver dessa grundläggande resurser bör du även överväga dessa valfria resurser:

- Nätverkssäkerhetsgrupper
- Belastningsutjämnare 

## <a name="network-interfaces"></a>Nätverksgränssnitt

Ett [nätverksgränssnitt (NIC)](../../virtual-network/virtual-network-network-interface.md) är gränssnittet mellan en virtuell dator och ett virtuellt nätverk (VNet). En virtuell dator måste ha minst ett NIC, men kan ha flera, beroende på storleken på den virtuella dator som du skapar. Lär dig mer om hur många NICs varje virtuell datorstorlek stöder i [Storlekar för virtuella datorer i Azure](sizes.md). 

Om du vill skapa en virtuell dator med mer än ett NIC måste du skapa den virtuella datorn med minst två.  När den har skapats kan du lägga till ytterligare NICs, upp till det antal som stöds av den virtuella datorns storlek, men du kan inte lägga till fler NICs till en virtuell dator som skapats med bara ett, oavsett hur många NICs som den virtuella datorns storlek stöder. 

Om den virtuella datorn läggs till i en tillgänglighetsuppsättning måste alla virtuella datorer inom tillgänglighetsuppsättningen ha antingen ett enda NIC eller flera NICs. Virtuella datorer med mer än ett NIC måste inte ha samma antal NICs, men de måste ha minst två.

Varje NIC som är kopplat till en virtuell dator måste befinna sig på samma plats och prenumeration som den virtuella datorn. Varje NIC måste anslutas till ett VNet som finns på samma Azure-plats och i samma Azure-prenumeration som denna NIC. När nätverkskortet har skapats kan du ändra undernätet som det är anslutet till, men du kan inte ändra det virtuella nätverket som det är anslutet till.  Varje NIC som är kopplad till en virtuell dator är tilldelad en MAC-adress som inte ändras förrän den virtuella datorn tas bort.

Den här tabellen anger de metoder som du kan använda för att skapa ett nätverksgränssnitt.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | När du skapar en virtuell dator i Azure Portal skapas automatiskt ett nätverksgränssnitt åt dig (du kan inte använda ett NIC som du skapar separat). Portalen skapar en virtuell dator med bara ett NIC. Om du vill skapa en virtuell dator med mer än ett NIC måste du skapa det med en annan metod. |
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md) | Använd [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkInterface) med parametern **-PublicIpAddressId** för att ange identifierare av den offentliga IP-adress som du skapade tidigare. |
| [Azure CLI](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md) | För att ange identifierare av den offentliga IP-adress som du skapade tidigare använder du [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) med parametern **--public-ip-address**. |
| [Mall](../../virtual-network/virtual-network-deploy-multinic-arm-template.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera ett nätverksgränssnitt med en mall. |

## <a name="ip-addresses"></a>IP-adresser 

Du kan tilldela dessa typer av [IP-adresser](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) till ett NIC i Azure:

- **Offentliga IP-adresser** – Används för att kommunicera inkommande och utgående (utan nätverksadressöversättning (NAT)) med Internet och andra Azure-resurser som inte är anslutna till ett VNet. Tilldelning av en offentlig IP-adress till ett NIC är valfritt. Offentliga IP-adresser har en nominell kostnad och det finns ett högsta antal som kan användas per prenumeration.
- **Privata IP-adresser** – Används för kommunikation inom ett VNet, dina lokala nätverk och Internet (med NAT). Du måste tilldela minst en privat IP-adress till en virtuell Dator. Mer information om NAT i Azure finns i [Förstå utgående anslutningar i Azure](../../load-balancer/load-balancer-outbound-connections.md).

Du kan tilldela offentliga IP-adresser till virtuella datorer eller internetuppkopplade belastningsutjämnare. Du kan tilldela privata IP-adresser till virtuella datorer och interna belastningsutjämnare. Du tilldelar IP-adresser till en virtuell dator med hjälp av ett nätverksgränssnitt.

En IP-adress kan tilldelas till en resurs med två metoder: dynamisk eller statisk allokering. Standardallokeringsmetoden är dynamisk, där IP-adressen inte tilldelas när den skapas. I stället tilldelas IP-adressen när du skapar en virtuell dator eller startar en stoppad virtuell dator. IP-adressen frisläpps när du stoppar eller tar bort den virtuella datorn. 

Om du inte vill att IP-adressen för den virtuella datorn ska ändras kan du uttryckligen ange allokeringsmetoden till statisk. I så fall tilldelas en IP-adress direkt. Den frisläpps bara om du ta bort den virtuella datorn eller om du ändrar dess allokeringsmetod till dynamisk.
    
Den här tabellen anger de metoder som du kan använda för att skapa en IP-adress.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure Portal](../../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Som standard är offentliga IP-adresser dynamiska och den adress som är associerad med dem kan ändras när den virtuella datorn har stoppats eller tagits bort. För att garantera att den virtuella datorn alltid använder samma offentliga IP-adress skapar du en statisk offentlig IP-adress. Som standard tilldelar portalen en dynamisk privat IP-adress till ett NIC när du skapar en virtuell dator. Du kan ändra den till statiskt när den virtuella datorn skapas.|
| [Azure PowerShell](../../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Du använder [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmPublicIpAddress) med parametern **- AllocationMethod** som dynamisk eller statisk. |
| [Azure CLI](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Du använder [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) med parametern **--allocation-method** som dynamisk eller statisk. |
| [Mall](../../virtual-network/virtual-network-deploy-static-pip-arm-template.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera en offentlig IP-adress med en mall. |

När du skapat en offentlig IP-adress kan du associera den med en virtuell dator genom att tilldela den ett NIC.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät

Ett undernät är ett intervall med IP-adresser i VNet. Du kan dela upp ett VNet i flera undernät av organisations- och säkerhetsskäl. Varje NIC i en virtuell Dator är ansluten till ett undernät i ett VNet. NICs som är anslutna till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration.

När du installerar ett VNet anger du topologin, inklusive tillgängliga adressutrymmen och undernät. Om VNet ska anslutas till andra VNets eller lokala nätverk måste du välja adressintervall som inte överlappar varandra. IP-adresser är privata och inte kan nås från Internet, vilket endast gäller för icke-dirigerbara IP-adresser som 10.0.0.0/8, 172.16.0.0/12 eller 192.168.0.0/16. Azure behandlar nu alla adressintervall som en del av det privata VNet:ets IP-adressutrymme som endast kan nås inom VNet, inom sammankopplade VNets och från din lokala plats. 

Om du arbetar inom en organisation där någon annan ansvarar för interna nätverk ska du tala med denna person innan du väljer ditt adressutrymme. Kontrollera att det inte finns någon överlappning och meddela vilket utrymme som du vill använda, så att ingen använder samma IP-adressintervall. 

Som standard finns ingen säkerhetsgräns mellan undernät, så virtuella datorer i vart och ett av dessa undernät kan kommunicera med varandra. Du kan dock konfigurera nätverkssäkerhetsgrupper (NSGs), så att du kan styra trafikflödet till och från undernät och till och från virtuella datorer. 

Den här tabellen anger de metoder som du kan använda för att skapa ett VNet och undernät.    

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure Portal](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md) | Om du låter Azure skapa ett VNet när du skapar en virtuell dator är namnet en kombination av resursgruppens namn som innehåller VNet och **-vnet**. Adressutrymmet är 10.0.0.0/24, det nödvändiga undernätets namn är **standard**, och adressintervallet för undernätet är 10.0.0.0/24. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md) | Du använder [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetworkSubnetConfig) och [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmVirtualNetwork) för att skapa ett undernät och ett VNet. Du kan också använda [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Add-AzureRmVirtualNetworkSubnetConfig) för att lägga till ett undernät i ett befintligt VNet. |
| [Azure CLI](../../virtual-network/virtual-networks-create-vnet-arm-cli.md) | Undernät och VNet skapas på samma gång. Ange en **--subnet-name**-parameter till [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) med namnet på undernätet. |
| [Mall](../../virtual-network/virtual-networks-create-vnet-arm-template-click.md) | Det enklaste sättet att skapa ett VNet och undernät är att hämta en befintlig mall, som till exempel [Virtuellt nätverk med två undernät](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), och modifiera den efter dina behov. |

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En [Nätverkssäkerhetsgrupp (NSG)](../../virtual-network/virtual-networks-nsg.md) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till undernät, NICs eller både och. NSG:er kan antingen associeras med undernät eller individuella NICs anslutna till ett undernät. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla virtuella datorer i det undernätet. Dessutom kan trafik till en enskild NIC begränsas genom att koppla en NSG direkt till en NIC.

NSG:er innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. Varje regel har egenskaperna för protokoll, källa och målportintervall, adressprefix, trafikriktning, prioritet och behörighetstyp. 

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar. 

När du kopplar en NSG till ett nätverkskort, tillämpas nätverksåtkomstreglerna för den NSG:n bara på det nätverkskortet. Om en NSG används för ett enda NIC på en virtuell multi-NIC-dator påverkas inte trafiken till andra NICs. Du kan koppla olika NSG:er till ett NIC (eller virtuell dator, beroende på distributionsmodell) och de undernät som ett NIC eller en virtuell dator är bunden till. Prioritering beror på trafikriktningen.

Se till att [planera](../../virtual-network/virtual-networks-nsg.md#planning) dina NSGs när du planerar din virtuella datorer och VNet.

Den här tabellen anger de metoder som du kan använda för att skapa en nätverkssäkerhetsgrupp.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure Portal](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md) | När du skapar en virtuell dator i Azure-portalen skapas en NSG automatiskt och kopplas till det NIC som portalen skapar. Namnet på denna NSG är en kombination av namnet på den virtuella datorn och **-nsg**. Denna NSG innehåller en inkommande regel med prioritet 1000, tjänst inställd på RDP, protokoll inställt på TCP, port inställd på 3389 och åtgärd inställd på Tillåt. Om du vill tillåta annan inkommande trafik till den virtuella datorn måste du lägga till ytterligare regler i NSG. |
| [Azure PowerShell](../../virtual-network/virtual-networks-create-nsg-arm-ps.md) | Använd [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityRuleConfig) och ange nödvändig regelinformation. Använd [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmNetworkSecurityGroup) för att skapa NSG. Använd [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetworkSubnetConfig) för att konfigurera NSG för undernätet. Använd [Set AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/Set-AzureRmVirtualNetwork?redirectedfrom=msdn) för att lägga till NSG till VNet. |
| [Azure CLI](../../virtual-network/virtual-networks-create-nsg-arm-cli.md) | Använd [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) för att först skapa NSG. Använd [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) för att lägga till regler i NSG. Använd [az network vnet subnet update](https://docs.microsoft.com/en-us/cli/azure/network/vnet/subnet#update) för att lägga till NSG i undernätet. |
| [Mall](../../virtual-network/virtual-networks-create-nsg-arm-template.md) | Använd [Skapa en nätverkssäkerhetsgrupp ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) som en vägledning för att distribuera en säkerhetsgrupp i nätverket med hjälp av en mall. |

## <a name="load-balancers"></a>Belastningsutjämnare

[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) levererar hög tillgänglighet och nätverksprestanda till dina program. En belastningsutjämnare kan konfigureras för att [balansera inkommande internettrafik](../../load-balancer/load-balancer-internet-overview.md) till virtuella datorer eller [balansera trafik mellan virtuella datorer i ett VNet](../../load-balancer/load-balancer-internal-overview.md). En belastningsutjämnare kan även balansera trafik mellan lokala datorer och virtuella datorer mellan lokala nätverk eller vidarebefordra extern trafik till en specifik virtuell dator.

Belastningsutjämnaren mappar inkommande och utgående trafik mellan den offentlig IP-adressen och porten på belastningsutjämnaren och den privata IP-adressen och porten för den virtuella datorn.

Du måste också beakta dessa konfigurationselement när du skapar en belastningsutjämnare:

- **Frontend IP-konfiguration** – En belastningsutjämnare kan innehålla en eller flera frontend IP-adresser, även kallat virtuella IP-adresser (VIPs). Dessa IP-adresser fungerar som ingresser för trafiken.
- **Backend adresspool** – IP-adresser som är associerade med det NIC till vilket belastningen distribueras.
- **NAT-regler** – Definierar hur inkommande trafik flödar genom frontend IP-adressen och distribueras till backend IP-adressen.
- **Belastningsutjämnarregler** – Mappar en given frontend-IP och portkombination till en uppsättning med backend-IP-adresser och portkombinationer. En enskild belastningsutjämnare kan ha flera regler för belastningsutjämning. Varje regel är en kombination av en frontend-IP och port och backend-IP och port som associeras med virtuella datorer.
- **[Probes](../../load-balancer/load-balancer-custom-probe-overview.md)** – Övervakar hälsotillståndet för virtuella datorer. När en avsökning inte svarar slutar belastningsutjämnaren att skicka nya anslutningar till den ohälsosamma virtuella datorn. Befintliga anslutningar påverkas inte och nya anslutningar skickas till felfria virtuella datorer.

Den här tabellen anger de metoder som du kan använda för att skapa en internetuppkopplad belastningsutjämnare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | Du kan för närvarande inte skapa en internetuppkopplad belastningsutjämnare med hjälp av Azure-portalen. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-internet-arm-ps.md) | För att ange identifierare för den offentliga IP-adress som du skapade tidigare använder du [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) med parametern **-PublicIpAddress**. Använd [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) för att skapa konfigurationen för backend-adresspoolen. Använd [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) för att skapa inkommande NAT-regler som är associerade med den frontend IP-konfiguration som du skapade. Använd [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) för att skapa de avsökningar som du behöver. Använd [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) för att skapa belastningsutjämningskonfigurationen. Använd [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../../load-balancer/load-balancer-get-started-internet-arm-cli.md) | Använd [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) för att skapa den första konfigurationen för belastningsutjämnaren. Använd [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) för att lägga till de offentliga IP-adresser som du skapade tidigare. Använd [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) för att lägga till NAT-regler. Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) för att lägga till regler för belastningsutjämnaren. Använd [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) för att lägga till avsökningar. |
| [Mall](../../load-balancer/load-balancer-get-started-internet-arm-template.md) | Använd [2 virtuella datorer i en belastningsutjämnare och konfigurera NAT-regler på LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) som en vägledning för att distribuera en belastningsutjämnare med en mall. |
    
Den här tabellen anger de metoder som du kan använda för att skapa en intern belastningsutjämnare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | Du kan för närvarande inte skapa en intern belastningsutjämnare med hjälp av Azure-portalen. |
| [Azure PowerShell](../../load-balancer/load-balancer-get-started-ilb-arm-ps.md) | För att tillhandahålla en privat IP-adress i nätverkets undernät använder du [New-AzureRmLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerFrontendIpConfig) tillsammans med parametern **-PrivateIpAddress**. Använd [New-AzureRmLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerBackendAddressPoolConfig) för att skapa konfigurationen för backend-adresspoolen. Använd [New-AzureRmLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerInboundNatRuleConfig) för att skapa inkommande NAT-regler som är associerade med den frontend IP-konfiguration som du skapade. Använd [New-AzureRmLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerProbeConfig) för att skapa de avsökningar som du behöver. Använd [New-AzureRmLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancerRuleConfig) för att skapa belastningsutjämningskonfigurationen. Använd [New-AzureRmLoadBalancer](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Network/v1.0.13/New-AzureRmLoadBalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../../load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Använd kommandot [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#create) för att skapa den första konfigurationen för belastningsutjämnaren. För att definiera den privata IP-adressen använder du [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip#create) med parametern **--private-ip-address** parameter. Använd [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool#create) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#create) för att lägga till NAT-regler. Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#create) för att lägga till regler för belastningsutjämnaren. Använd [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#create) för att lägga till avsökningar.|
| [Mall](../../load-balancer/load-balancer-get-started-ilb-arm-template.md) | Använd [2 virtuella datorer i en belastningsutjämnare och konfigurera NAT-regler på LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) som en vägledning för att distribuera en belastningsutjämnare med en mall. |

## <a name="vms"></a>Virtuella datorer

Virtuella datorer kan skapas i samma VNet och de kan ansluta till varandra med privata IP-adresser. De kan ansluta även om de finns i olika undernät utan att en gateway måste konfigureras eller offentliga IP-adresser användas. För att placera virtuella datorer i ett VNet skapar du detta VNet, och sedan när du skapar varje virtuell dator tilldelar du den till VNet och undernät. Virtuella datorer får sina nätverksinställningar under distributionen eller starten.  

Virtuella datorer tilldelats en IP-adress när de distribueras. Om du distribuerar flera virtuella datorer i ett VNet eller ett undernät, tilldelas de IP-adresser när de startas. En dynamisk IP-adress (DIP) är den interna IP-adress som är associerad med en virtuell dator. Du kan allokera en statisk DIP till en virtuell dator. Om du allokerar en statisk DIP bör du överväga att använda ett specifikt undernät för att undvika att av misstag återanvända en statisk DIP för en annan virtuell dator.  

Om du skapar en virtuell dator och vill migrera den till ett VNet är detta inte någon enkel konfigurationsändring. Du måste distribuera om den virtuella datorn till VNet. Det enklaste sättet att distribuera om är att ta bort den virtuella datorn, men inte eventuella diskar som är anslutna till den, och därefter skapa om den virtuella datorn i VNet med hjälp av de ursprungliga diskarna. 

Den här tabellen anger de metoder som du kan använda för att skapa en virtuell dator i ett VNet.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure Portal](../virtual-machines-windows-hero-tutorial.md) | Använder standardinställningarna för nätverk som nämndes tidigare för att skapa en virtuell dator med ett enskilt NIC. Om du vill skapa en virtuell dator med flera NICs måste du använda en annan metod. |
| [Azure PowerShell](../virtual-machines-windows-ps-create.md) | Inkluderar användningen av [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v1.3.4/Add-AzureRmVMNetworkInterface) för att lägga till det NIC som du skapade tidigare i konfigurationen av den virtuella datorn. |
| [Mall](ps-template.md) | Använd [Mycket enkel distribution av virtuella Windows-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) som en vägledning för att distribuera en virtuell dator med hjälp av en mall. |

## <a name="next-steps"></a>Nästa steg

- Läs mer om konfigurering av [användardefinierade vägar och IP-vidarebefordring](../../virtual-network/virtual-networks-udr-overview.md). 
- Läs mer om hur du konfigurerar [VNet-till-VNet-anslutningar](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Läs mer om [Felsökningsvägar](../../virtual-network/virtual-network-routes-troubleshoot-portal.md).

