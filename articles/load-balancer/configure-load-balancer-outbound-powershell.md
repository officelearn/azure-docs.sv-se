---
title: Konfigurera belastningsutjämning och utgående regler med hjälp av Azure PowerShell
titleSuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastningsutjämning och utgående regler i Standard belastningsutjämning med hjälp av Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225431"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurera belastningsutjämning och utgående regler i Standard load balancer med hjälp av Azure PowerShell

Den här artikeln visar hur du konfigurerar utgående regler i Standard load balancer med hjälp av Azure PowerShell.  

När du är klar med den här artikelns scenario innehåller belastningsutjämnarresursen två främre ändar och tillhörande regler. Du har en frontend för inkommande trafik och en annan frontend för utgående trafik.  

Varje klientreferens refererar till en offentlig IP-adress. I det här fallet skiljer sig den offentliga IP-adressen för inkommande trafik från adressen för utgående trafik.   Belastningsutjämningsregeln ger endast ingående belastningsutjämning. Den utgående regeln styr den utgående nätverksadressöversättningen (NAT) för den virtuella datorn.  

Scenariot använder två backend-pooler: en för inkommande trafik och en för utgående trafik. Dessa pooler illustrerar kapacitet och ger flexibilitet för scenariot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto
Logga in på din Azure-prenumeration med hjälp av kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Följ sedan anvisningarna på skärmen.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med hjälp av [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). En Azure-resursgrupp är en logisk behållare som Azure-resurser distribueras till. Resurserna hanteras sedan från gruppen.

I följande exempel skapas en resursgrupp med namnet *myresourcegroupoutbound på* *eastus2-platsen:*

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med namnet *myvnetoutbound*. Namnge dess undernät *mysubnetoutbound*. Placera den i *myresourcegroupoutbound* med hjälp av [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) och [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Skapa en inkommande offentlig IP-adress 

För att komma åt din webbapp på internet behöver du en offentlig IP-adress för belastningsutjämnaren. Standardbelastningsutjämnare stöder endast vanliga offentliga IP-adresser. 

Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) för att skapa en vanlig offentlig IP-adress med namnet *mypublicipinbound* i *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Skapa en utgående offentlig IP-adress 

Skapa en standard-IP-adress för belastningsutjämnarens front-end utgående konfiguration med hjälp av [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>skapa en Azure Load Balancer

I det här avsnittet beskrivs hur du skapar och konfigurerar följande komponenter i belastningsutjämnaren:
  - En front-end IP som tar emot inkommande nätverkstrafik på lastbalanseraren
  - En backend-pool där front-end IP skickar belastningsbalanserad nätverkstrafik
  - En backend-pool för utgående anslutning
  - En hälsoavsökning som avgör hälsotillståndet för backend-VM-instanser
  - En inlastbalanseringsregel som definierar hur trafiken fördelas till de virtuella datorerna
  - En utgående last-balancer-regel som definierar hur trafiken fördelas från de virtuella datorerna

### <a name="create-an-inbound-front-end-ip"></a>Skapa en inkommande frontend-IP
Skapa den inkommande frontend-IP-konfigurationen för belastningsutjämnaren med hjälp av [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Belastningsutjämnaren bör innehålla en inkommande ip-konfiguration med namnet *myfrontendinbound*. Associera den här konfigurationen med den offentliga IP-adressen *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Skapa en utgående frontend-IP
Skapa den utgående IP-konfigurationen för belastningsutjämnaren med hjälp av [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Den här belastningsutjämnaren bör innehålla en utgående IP-konfiguration med namnet *myfrontendoutbound*. Associera den här konfigurationen med den offentliga IP-adressen *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Skapa en inkommande backend-pool
Skapa den bakre inkommande poolen för belastningsutjämnaren med hjälp av [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Ge poolen namnet *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Skapa en utgående backend-pool
Använd följande kommando för att skapa en annan backend-adresspool för att definiera utgående anslutning för en pool med virtuella datorer med hjälp av [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Ge den här poolen *namnet.* 

Genom att skapa en separat utgående pool ger du maximal flexibilitet. Men du kan utelämna det här steget och bara använda inkommande *bepoolinbound* om du föredrar.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

En hälsoavsökning kontrollerar alla VM-instanser för att se till att de kan skicka nätverkstrafik. Den VM-instans som misslyckas med avsökningskontroller tas bort från belastningsutjämnaren tills den går online igen och en avsökningskontroll avgör att den är felfri. 

Om du vill övervaka hälsotillståndet för de virtuella datorerna skapar du en hälsoavsökning med hjälp av [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Skapa en belastningsutjämnareregel

En belastningsutjämnadsregel definierar frontend-IP-konfigurationen för inkommande trafik och backend-poolen för att ta emot trafiken. Den definierar också den nödvändiga käll- och målporten. 

Skapa en belastningsutjämnad regel med namnet *myinboundlbrule* med hjälp av [New-AzLoadBalancerRRUleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Denna regel kommer att lyssna på port 80 i front-end pool *myfrontendinbound*. Den kommer också att använda port 80 för att skicka belastningsbalanserad nätverkstrafik till backend-adresspoolen *som är inkommande*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Den här belastningsutjämningsregeln inaktiverar automatisk utgående säker NAT (SNAT) på grund av parametern **-DisableOutboundSNAT.** Utgående NAT tillhandahålls endast av den utgående regeln.

### <a name="create-an-outbound-rule"></a>Skapa en regel för utgående trafik

En utgående regel definierar den offentliga IP-adressen för frontend, som representeras av *frontend-myfrontendoutbound*. Den här klientdelen används för all utgående NAT-trafik samt den backend-pool som regeln gäller för.  

Använd följande kommando för att skapa en utgående regel *myoutboundrule* för utgående nätverksöversättning av alla virtuella datorer (i NIC IP-konfigurationer) i *bepool* backend-poolen.  Kommandot ändrar den utgående inaktiva time-out-time-out från 4 till 15 minuter. Den fördelar 10 000 SNAT-portar i stället för 1 024. Mer information finns i [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Om du inte vill använda en separat utgående pool kan du ändra argumentet för adresspool i föregående kommando för att ange *$bepoolin* i stället.  Vi rekommenderar att du använder separata pooler för att göra den resulterande konfigurationen flexibel och läsbar.

### <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Använd följande kommando för att skapa en belastningsutjämnare för den inkommande IP-adressen med hjälp av [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Namnge belastningsutjämnaren *lb*. Den bör innehålla en inkommande ip-konfiguration för frontend. Dess backend-pool *bepoolinbound* bör associeras med den offentliga IP-adressen *mypublicipinbound* som du skapade i föregående steg.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Nu kan du fortsätta att lägga till dina virtuella datorer i både *bepoolinbound* och *bepooloutbound* backend-pooler genom att uppdatera IP-konfigurationen för respektive nätverkskortsresurser. Uppdatera resurskonfigurationen med Hjälp av [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver resursgruppen, belastningsutjämnaren och relaterade resurser kan du ta bort dem med hjälp av [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln skapade du en standardbelastningsutjämnare, konfigurerade trafikregler för både inkommande och utgående belastningsutjämning och konfigurerade en hälsoavsökning för de virtuella datorerna i backend-poolen. 

Om du vill veta mer fortsätter du till [självstudierna för Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
