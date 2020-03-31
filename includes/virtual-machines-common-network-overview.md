---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9e6eafc4e2f6ae4a0cf1d99cb63bfed53db77f69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029186"
---
När du skapar en virtuell Azure-dator (VM) måste du skapa ett [virtuellt nätverk](../articles/virtual-network/virtual-networks-overview.md) (VNet) eller använda ett befintligt VNet. Du måste också bestämma hur dina virtuella datorer är avsedda att användas på VNet. Det är viktigt att [planera innan du skapar resurser](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) och att säkerställa att du förstår [begränsningarna för nätverksresurser](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

I följande bild visas virtuella datorer som webb- och databasservrar. Varje uppsättning virtuella datorer är tilldelade separata undernät i VNet.

![Azure-virtuellt nätverk](./media/virtual-machines-common-network-overview/vnetoverview.png)

Du kan skapa ett virtuellt nätverk innan du skapar en virtuell dator eller så kan du när du skapar en virtuell dator. Du skapar dessa resurser för att stödja kommunikation med en virtuell dator:

- Nätverksgränssnitt
- IP-adresser
- Virtuellt nätverk och undernät

Utöver dessa grundläggande resurser bör du även överväga dessa valfria resurser:

- Nätverkssäkerhetsgrupper
- Lastbalanserare 

## <a name="network-interfaces"></a>Nätverksgränssnitt

Ett [nätverksgränssnitt (NIC)](../articles/virtual-network/virtual-network-network-interface.md) är gränssnittet mellan en virtuell dator och ett virtuellt nätverk (VNet). En virtuell dator måste ha minst ett NIC, men kan ha flera, beroende på storleken på den virtuella dator som du skapar. Lär dig mer om hur många nätverkskort som varje VM-storlek stöder för [Windows](../articles/virtual-machines/windows/sizes.md) eller [Linux](../articles/virtual-machines/linux/sizes.md).

Du kan skapa en virtuell dator med flera nätverkskort och lägga till eller ta bort nätverkskort via livscykeln för en virtuell dator. Flera nätverkskort gör det möjligt för en virtuell dator att ansluta till olika undernät och skicka eller ta emot trafik över det lämpligaste gränssnittet. Virtuella datorer med valfritt antal nätverksgränssnitt kan finnas i samma tillgänglighetsuppsättning, upp till det antal som stöds av den virtuella datorns storlek. 

Varje NIC som är kopplat till en virtuell dator måste befinna sig på samma plats och prenumeration som den virtuella datorn. Varje NIC måste anslutas till ett VNet som finns på samma Azure-plats och i samma Azure-prenumeration som denna NIC. Du kan ändra undernätet som en virtuell dator är ansluten till när det har skapats, men du kan inte ändra det virtuella nätverket. Varje NIC som är kopplad till en virtuell dator är tilldelad en MAC-adress som inte ändras förrän den virtuella datorn tas bort.

Den här tabellen anger de metoder som du kan använda för att skapa ett nätverksgränssnitt.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | När du skapar en virtuell dator i Azure Portal skapas automatiskt ett nätverksgränssnitt åt dig (du kan inte använda ett NIC som du skapar separat). Portalen skapar en virtuell dator med bara ett NIC. Om du vill skapa en virtuell dator med mer än ett NIC måste du skapa det med en annan metod. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Använd [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) med parametern **-PublicIpAddressId** för att ange identifieraren för den offentliga IP-adress som du tidigare skapade. |
| [Azure CLI](../articles/virtual-machines/linux/multiple-nics.md) | Om du vill ange identifieraren för den offentliga IP-adress som du tidigare skapade använder du [az-nätverksnic create](https://docs.microsoft.com/cli/azure/network/nic) med parametern **--public-ip-address.** |
| [Mall](../articles/virtual-network/template-samples.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera ett nätverksgränssnitt med en mall. |

## <a name="ip-addresses"></a>IP-adresser 

Du kan tilldela dessa typer av [IP-adresser](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) till ett NIC i Azure:

- **Offentliga IP-adresser** – Används för att kommunicera inkommande och utgående (utan nätverksadressöversättning (NAT)) med Internet och andra Azure-resurser som inte är anslutna till ett VNet. Tilldelning av en offentlig IP-adress till ett NIC är valfritt. Offentliga IP-adresser har en nominell avgift och det finns ett maximalt antal som kan användas per prenumeration.
- **Privata IP-adresser** – Används för kommunikation inom ett VNet, dina lokala nätverk och Internet (med NAT). Du måste tilldela minst en privat IP-adress till en virtuell Dator. Mer information om NAT i Azure finns i [Förstå utgående anslutningar i Azure](../articles/load-balancer/load-balancer-outbound-connections.md).

Du kan tilldela offentliga IP-adresser till virtuella datorer eller internetuppkopplade lastbalanserare. Du kan tilldela privata IP-adresser till virtuella datorer och interna lastbalanserare. Du tilldelar IP-adresser till en virtuell dator med hjälp av ett nätverksgränssnitt.

En IP-adress kan tilldelas till en resurs med två metoder: dynamisk eller statisk allokering. Standardallokeringsmetoden är dynamisk, där IP-adressen inte tilldelas när den skapas. I stället tilldelas IP-adressen när du skapar en virtuell dator eller startar en stoppad virtuell dator. IP-adressen frisläpps när du stoppar eller tar bort den virtuella datorn. 

Om du inte vill att IP-adressen för den virtuella datorn ska ändras kan du uttryckligen ange allokeringsmetoden till statisk. I så fall tilldelas en IP-adress direkt. Den frisläpps bara om du ta bort den virtuella datorn eller om du ändrar dess allokeringsmetod till dynamisk.
    
Den här tabellen anger de metoder som du kan använda för att skapa en IP-adress.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portal](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Som standard är offentliga IP-adresser dynamiska och den adress som är associerad med dem kan ändras när den virtuella datorn har stoppats eller tagits bort. För att garantera att den virtuella datorn alltid använder samma offentliga IP-adress skapar du en statisk offentlig IP-adress. Som standard tilldelar portalen en dynamisk privat IP-adress till ett NIC när du skapar en virtuell dator. Du kan ändra den här IP-adressen till statisk när den virtuella datorn har skapats.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Du använder [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) med parametern **-AllocationMethod** som dynamisk eller statisk. |
| [Azure CLI](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Du använder [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) med parametern **--allocation-method** som dynamisk eller statisk. |
| [Mall](../articles/virtual-network/template-samples.md) | Använd [Nätverksgränssnitt i ett virtuellt nätverk med offentliga IP-adresser](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) som en vägledning för att distribuera en offentlig IP-adress med en mall. |

När du skapat en offentlig IP-adress kan du associera den med en virtuell dator genom att tilldela den ett NIC.

## <a name="virtual-network-and-subnets"></a>Virtuellt nätverk och undernät

Ett undernät är ett intervall med IP-adresser i VNet. Du kan dela upp ett VNet i flera undernät av organisations- och säkerhetsskäl. Varje NIC i en virtuell Dator är ansluten till ett undernät i ett VNet. NICs som är anslutna till undernät (samma eller olika) inom ett VNet, kan kommunicera med varandra utan övrig konfiguration.

När du installerar ett VNet anger du topologin, inklusive tillgängliga adressutrymmen och undernät. Om VNet ska anslutas till andra VNets eller lokala nätverk måste du välja adressintervall som inte överlappar varandra. IP-adresserna är privata och kan inte nås från Internet, vilket endast var sant för icke-dirigerbara IP-adresser som 10.0.0.0/8, 172.16.0.0/12 eller 192.168.0.0/16. Azure behandlar nu alla adressintervall som en del av det privata VNet:ets IP-adressutrymme som endast kan nås inom VNet, inom sammankopplade VNets och från din lokala plats. 

Om du arbetar inom en organisation där någon annan ansvarar för interna nätverk ska du tala med denna person innan du väljer ditt adressutrymme. Kontrollera att det inte finns någon överlappning och meddela vilket utrymme som du vill använda, så att ingen använder samma IP-adressintervall. 

Som standard finns ingen säkerhetsgräns mellan undernät, så virtuella datorer i vart och ett av dessa undernät kan kommunicera med varandra. Du kan dock konfigurera nätverkssäkerhetsgrupper (NSGs), så att du kan styra trafikflödet till och från undernät och till och från virtuella datorer. 

Den här tabellen anger de metoder som du kan använda för att skapa ett VNet och undernät. 

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portal](../articles/virtual-network/quick-create-portal.md) | Om du låter Azure skapa ett VNet när du skapar en virtuell dator är namnet en kombination av resursgruppens namn som innehåller VNet och **-vnet**. Adressutrymmet är 10.0.0.0/24, det nödvändiga undernätets namn är **standard**, och adressintervallet för undernätet är 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | Du använder [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) och [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) för att skapa ett undernät och ett virtuella nätverk. Du kan också använda [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) för att lägga till ett undernät i ett befintligt virtuella nätverk. |
| [Azure CLI](../articles/virtual-network/quick-create-cli.md) | Undernät och VNet skapas på samma gång. Ange en **--subnet-name**-parameter till [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) med namnet på undernätet. |
| Mall | Det enklaste sättet att skapa ett virtuellt nätverk och undernät är att hämta en befintlig mall, till exempel [virtuellt nätverk med två undernät,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)och ändra den för dina behov. |

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En [Nätverkssäkerhetsgrupp (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till undernät, NICs eller både och. NSG:er kan antingen associeras med undernät eller individuella NICs anslutna till ett undernät. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla virtuella datorer i det undernätet. Dessutom kan trafik till en enskild NIC begränsas genom att koppla en NSG direkt till en NIC.

NSG:er innehåller två regeluppsättningar: inkommande och utgående. En regels prioritet måste vara unik inom varje uppsättning. Varje regel har egenskaperna för protokoll, källa och målportintervall, adressprefix, trafikriktning, prioritet och behörighetstyp. 

Alla NSG:er har en uppsättning standardregler. Standardreglerna kan inte tas bort, men eftersom de tilldelas lägst prioritet så kan de överskridas av de reglerna du själv skapar. 

När du kopplar en NSG till ett nätverkskort, tillämpas nätverksåtkomstreglerna för den NSG:n bara på det nätverkskortet. Om en NSG används för ett enda NIC på en virtuell multi-NIC-dator påverkas inte trafiken till andra NICs. Du kan koppla olika NSG:er till ett NIC (eller virtuell dator, beroende på distributionsmodell) och de undernät som ett NIC eller en virtuell dator är bunden till. Prioritering beror på trafikriktningen.

Se till att [planera](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) dina NSGs när du planerar din virtuella datorer och VNet.

Den här tabellen anger de metoder som du kan använda för att skapa en nätverkssäkerhetsgrupp.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portal](../articles/virtual-network/tutorial-filter-network-traffic.md) | När du skapar en virtuell dator i Azure-portalen skapas en NSG automatiskt och kopplas till det NIC som portalen skapar. Namnet på denna NSG är en kombination av namnet på den virtuella datorn och **-nsg**. Denna NSG innehåller en inkommande regel med prioritet 1000, tjänst inställd på RDP, protokoll inställt på TCP, port inställd på 3389 och åtgärd inställd på Tillåt. Om du vill tillåta annan inkommande trafik till den virtuella datorn måste du lägga till ytterligare regler i NSG. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Använd [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig) och ange den regelinformation som krävs. Använd [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) för att skapa NSG. Använd [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) för att konfigurera NSG för undernätet. Använd [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) för att lägga till NSG i det virtuella nätverket. |
| [Azure CLI](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | Använd [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) för att först skapa NSG. Använd [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) för att lägga till regler i NSG. Använd [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet) för att lägga till NSG i undernätet. |
| [Mall](../articles/virtual-network/template-samples.md) | Använd [Skapa en nätverkssäkerhetsgrupp ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) som en vägledning för att distribuera en säkerhetsgrupp i nätverket med hjälp av en mall. |

## <a name="load-balancers"></a>Lastbalanserare

[Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) levererar hög tillgänglighet och nätverksprestanda till dina program. En lastbalanserare kan konfigureras för att [balansera inkommande internettrafik](../articles/load-balancer/load-balancer-internet-overview.md) till virtuella datorer eller [balansera trafik mellan virtuella datorer i ett VNet](../articles/load-balancer/load-balancer-internal-overview.md). En lastbalanserare kan även balansera trafik mellan lokala datorer och virtuella datorer mellan lokala nätverk eller vidarebefordra extern trafik till en specifik virtuell dator.

Lastbalanseraren mappar inkommande och utgående trafik mellan den offentlig IP-adressen och porten på lastbalanseraren och den privata IP-adressen och porten för den virtuella datorn.

Du måste också beakta dessa konfigurationselement när du skapar en lastbalanserare:

- **Frontend IP-konfiguration** – En lastbalanserare kan innehålla en eller flera frontend IP-adresser, även kallat virtuella IP-adresser (VIPs). Dessa IP-adresser fungerar som ingresser för trafiken.
- **Backend adresspool** – IP-adresser som är associerade med det NIC till vilket belastningen distribueras.
- **NAT-regler** – Definierar hur inkommande trafik flödar genom frontend IP-adressen och distribueras till backend IP-adressen.
- **Lastbalanseringsregler** – Mappar en given frontend-IP och portkombination till en uppsättning med backend-IP-adresser och portkombinationer. En enskild lastbalanserare kan ha flera regler för lastbalansering. Varje regel är en kombination av en frontend-IP och port och backend-IP och port som associeras med virtuella datorer.
- **[Probes](../articles/load-balancer/load-balancer-custom-probe-overview.md)** – Övervakar hälsotillståndet för virtuella datorer. När en avsökning inte svarar slutar lastbalanseraren att skicka nya anslutningar till den ohälsosamma virtuella datorn. Befintliga anslutningar påverkas inte och nya anslutningar skickas till felfria virtuella datorer.

Den här tabellen anger de metoder som du kan använda för att skapa en internetuppkopplad lastbalanserare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal |  Du kan [läsa internettrafiken på virtuella datorer med hjälp av Azure-portalen](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](/azure/load-balancer/load-balancer-get-started-ilb-arm-ps) | Om du vill ange identifieraren för den offentliga IP-adress som du tidigare skapade använder du [Parametern New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) med parametern **-PublicIpAddress.** Använd [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) för att skapa konfigurationen av backend-adresspoolen. Använd [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) för att skapa inkommande NAT-regler som är associerade med frontend IP-konfigurationen som du skapade. Använd [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) för att skapa de avsökningar som du behöver. Använd [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) för att skapa belastningsutjämnarens konfiguration. Använd [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Använd [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) för att skapa den första konfigurationen för lastbalanseraren. Använd [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) för att lägga till de offentliga IP-adresser som du skapade tidigare. Använd [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) för att lägga till NAT-regler. Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) för att lägga till regler för lastbalanseraren. Använd [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) för att lägga till avsökningar. |
| [Mall](../articles/load-balancer/quickstart-load-balancer-standard-public-template.md) | Använd [2 virtuella datorer i en lastbalanserare och konfigurera NAT-regler på LB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) som en vägledning för att distribuera en lastbalanserare med en mall. |
    
Den här tabellen anger de metoder som du kan använda för att skapa en intern lastbalanserare.

| Metod | Beskrivning |
| ------ | ----------- |
| Azure Portal | Du kan [balansera intern trafikbelastning med en grundläggande belastningsutjämnare i Azure-portalen](../articles/load-balancer/tutorial-load-balancer-basic-internal-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Om du vill ange en privat IP-adress i nätverksundernätet använder du [Parametern New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) med parametern **-PrivateIpAddress.** Använd [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) för att skapa konfigurationen av backend-adresspoolen. Använd [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) för att skapa inkommande NAT-regler som är associerade med frontend IP-konfigurationen som du skapade. Använd [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) för att skapa de avsökningar som du behöver. Använd [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) för att skapa belastningsutjämnarens konfiguration. Använd [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) för att skapa belastningsutjämnaren.|
| [Azure CLI](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Använd kommandot [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) för att skapa den första konfigurationen för lastbalanseraren. För att definiera den privata IP-adressen använder du [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) med parametern **--private-ip-address** parameter. Använd [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool) att lägga till konfigurationen av backend-adresspoolen. Använd [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) för att lägga till NAT-regler. Använd [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) för att lägga till regler för lastbalanseraren. Använd [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) för att lägga till avsökningar.|
| [Mall](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Använd [2 virtuella datorer i en lastbalanserare och konfigurera NAT-regler på LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) som en vägledning för att distribuera en lastbalanserare med en mall. |

## <a name="vms"></a>Virtuella datorer

Virtuella datorer kan skapas i samma VNet och de kan ansluta till varandra med privata IP-adresser. De kan ansluta även om de finns i olika undernät utan att en gateway måste konfigureras eller offentliga IP-adresser användas. För att placera virtuella datorer i ett VNet skapar du detta VNet, och sedan när du skapar varje virtuell dator tilldelar du den till VNet och undernät. Virtuella datorer får sina nätverksinställningar under distributionen eller starten.  

Virtuella datorer tilldelats en IP-adress när de distribueras. Om du distribuerar flera virtuella datorer i ett VNet eller ett undernät, tilldelas de IP-adresser när de startas. Du kan också allokera en statisk IP till en virtuell dator. Om du allokerar en statisk IP bör du överväga att använda ett visst undernät för att undvika att av misstag återanvända en statisk IP för en annan virtuell dator.  

Om du skapar en virtuell dator och vill migrera den till ett VNet är detta inte någon enkel konfigurationsändring. Du måste distribuera om den virtuella datorn till VNet. Det enklaste sättet att distribuera om är att ta bort den virtuella datorn, men inte eventuella diskar som är anslutna till den, och därefter skapa om den virtuella datorn i VNet med hjälp av de ursprungliga diskarna. 

Den här tabellen anger de metoder som du kan använda för att skapa en virtuell dator i ett VNet.

| Metod | Beskrivning |
| ------ | ----------- |
| [Azure-portal](../articles/virtual-machines/windows/quick-create-portal.md) | Använder standardinställningarna för nätverk som nämndes tidigare för att skapa en virtuell dator med ett enskilt NIC. Om du vill skapa en virtuell dator med flera NICs måste du använda en annan metod. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Inkluderar användningen av [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) för att lägga till nätverkskortet som du tidigare skapade i vm-konfigurationen. |
| [Azure CLI](../articles/virtual-machines/linux/create-cli-complete.md) | Skapa och anslut en virtuell dator till ett virtuellt nätverk, undernät och nätverkskort som skapas som enskilda steg. |
| [Mall](../articles/virtual-machines/windows/ps-template.md) | Använd [Mycket enkel distribution av virtuella Windows-datorer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) som en vägledning för att distribuera en virtuell dator med hjälp av en mall. |

## <a name="next-steps"></a>Nästa steg
För VM-specifika steg om hur du hanterar virtuella Azure-nätverk för virtuella datorer finns i självstudierna för [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) eller [Linux.](../articles/virtual-machines/linux/tutorial-virtual-network.md)

Det finns också handledning om hur man laddar balans virtuella datorer och skapa högtillgängliga program för [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) eller [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md).

- Läs mer om konfigurering av [användardefinierade vägar och IP-vidarebefordring](../articles/virtual-network/virtual-networks-udr-overview.md). 
- Läs mer om hur du konfigurerar [VNet-till-VNet-anslutningar](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).
- Läs mer om [Felsökningsvägar](../articles/virtual-network/diagnose-network-routing-problem.md).
- Läs mer om [bandbredd för virtuella datorer](../articles/virtual-network/virtual-machine-network-throughput.md).
