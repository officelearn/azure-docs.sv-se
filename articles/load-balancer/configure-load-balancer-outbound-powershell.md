---
title: Konfigurera belastnings utjämning och utgående regler med hjälp av Azure PowerShell
titlesuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastnings utjämning och utgående regler i en Standard Load Balancer med hjälp av Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 6e85d31e40e35b9d796fc66394a6eb446c7302ad
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262627"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-powershell"></a>Konfigurera belastnings utjämning och utgående regler i standard Load Balancer med hjälp av Azure PowerShell

Den här artikeln visar hur du konfigurerar utgående regler i en standard belastningsutjämnare med hjälp av Azure PowerShell.  

När du är färdig innehåller belastnings Utjämnings resursen två klient delar och regler som är kopplade till dem: en för inkommande och en annan för utgående.  Varje klientdel innehåller en referens till en offentlig IP-adress och det här scenariot använder en annan offentlig IP-adress för inkommande och utgående.   Belastningsutjämningsregeln ger endast belastningsutjämning inkommande och utgående regel styr utgående NAT för den virtuella datorn.  I den här artikeln används två separata backend-pooler, en för inkommande och en för utgående, för att illustrera kapacitet och tillåta flexibilitet för det här scenariot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-azure-account"></a>Anslut till Azure-konto
Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) och följ anvisningarna på skärmen:
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myresourcegroupoutbound* i den *usaöstra2* plats:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-virtual-network"></a>Skapa det virtuella nätverket
Skapa ett virtuellt nätverk med namnet *myvnetoutbound* med ett undernät med namnet *mysubnetoutbound* i *myresourcegroupoutbound* med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) och [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0):

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-inbound-public-ip-address"></a>Skapa offentlig IP-adress för inkommande trafik 

För att du ska kunna komma åt din webbapp på Internet behöver du en offentlig IP-adress för lastbalanseraren. En standard belastningsutjämnare stöder endast offentliga IP-adresser. Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) för att skapa en offentlig standard-IP-adress med namnet *mypublicipinbound* i *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-outbound-public-ip-address"></a>Skapa utgående offentliga IP-adress 

Skapa en standard-IP-adress för den utgående konfigurationen för belastningsutjämnarens klient del med [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-azure-load-balancer"></a>Skapa en Azure Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:
  - En frontend-IP som tar emot den inkommande nätverkstrafiken på belastningsutjämnaren.
  - En backend-pool där klient delens IP-adress skickar den belastningsutjämnade nätverks trafiken.
  - En backend-pool för utgående anslutning. 
  - en hälsoavsökning som fastställer hälsan för serverdelens Virtuella datorinstanser.
  - En inkommande regel för belastningsutjämnaren som definierar hur trafiken ska distribueras till de virtuella datorerna.
  - En utgående regel för belastningsutjämnaren som definierar hur trafiken ska distribueras från de virtuella datorerna.

### <a name="create-inbound-frontend-ip"></a>Skapa ingående IP-adress för klient del
Skapa IP-konfigurationen för utgående klient del för belastningsutjämnaren med [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) som innehåller en inkommande klient dels-IP-konfiguration med namnet *myfrontendinbound* som är kopplad till den offentliga IP-adressen  *mypublicipinbound*

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-outbound-frontend-ip"></a>Skapa utgående klientdels-IP
Skapa IP-konfigurationen för utgående klient del för belastningsutjämnaren med [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0) som innehåller en utgående IP-konfiguration för klient delen med namnet *myfrontendoutbound* som är kopplad till den offentliga IP-adressen  *mypublicipoutbound*:

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-inbound-backend-pool"></a>Skapa inkommande backend-pool
Skapa server delens inkommande pool för belastningsutjämnaren med [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) med namnet *bepoolinbound*:

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-outbound-backend-pool"></a>Skapa utgående backend-pool
Skapa ytterligare en backend-adresspool för att definiera utgående anslutning för en pool med virtuella datorer med [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0) med namnet *bepooloutbound*. Att skapa en separat utgående pool ger maximal flexibilitet, men du kan utelämna det här steget och endast använda inkommande *bepoolinbound* .  :

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-health-probe"></a>Skapa hälsoavsökning

En hälsoavsökning kontrollerar alla virtuella datorinstanser för att säkerställa att de kan skicka nätverkstrafik. Den virtuella datorinstansen med misslyckad hälsoavsökning tas bort från lastbalanseraren tills den är tillbaka online och en avsökningskontroll visar att den är felfri. Skapa en hälso avsökning med [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0) för att övervaka hälso tillståndet för de virtuella datorerna. 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-load-balancing-rule"></a>Skapa regel för belastningsutjämning

En belastningsutjämningsregel definierar klientdelens IP-konfiguration för inkommande trafik och backend-poolen för att ta emot trafik samt nödvändig käll- och port. Skapa en regel för belastnings utjämning *myinboundlbrule* med [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0) för att lyssna på port 80 i frontend-poolens *myfrontendinbound* och skicka belastningsutjämnad nätverks trafik till backend- *adresspoolen bepoolinbound* använder också port 80. 

>[!NOTE]
>Den här belastnings Utjämnings regeln inaktiverar automatisk utgående NAT som ett resultat av den här regeln med parametern **-DisableOutboundSNAT** . Utgående NAT tillhandahålls endast av regel för utgående trafik.

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

### <a name="create-outbound-rule"></a>Skapa regel för utgående trafik

En utgående regel som definierar den frontend offentlig IP-adress som representeras av klientdelen *myfrontendoutbound*, som används för all utgående NAT-trafik samt serverdelspoolen som regeln gäller.  Skapa en utgående regel *myoutboundrule* för utgående nätverkstrafik översättning av alla virtuella datorer (NIC IP-konfigurationer) i *bepool* serverdelspool.  Kommandot nedan även ändras utgående tidsgränsen för inaktivitet från 4 till 15 minuter och allokerar 10000 SNAT portar i stället för 1024.  Granska [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0) för mer information.

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Om du inte vill använda en separat utgående pool kan du ändra argumentet för adresspoolen i föregående kommando för att ange *$bepoolin* i stället.  Vi rekommenderar att du använder separata pooler för flexibilitet och läsbarhet för den resulterande konfigurationen.

### <a name="create-load-balancer"></a>Skapa lastbalanserare

Skapa en belastningsutjämnare med den inkommande IP-adressen med [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0) med namnet *lb* som innehåller en inkommande IP-konfiguration för klient delen och en *bepoolinbound* som är associerad med den offentliga IP-adressen  *mypublicipinbound* som du skapade i föregående steg.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Nu kan du fortsätta med att lägga till dina virtuella datorer i backend-poolens *bepoolinbound* __och__ *bepooloutbound* genom att uppdatera IP-konfigurationen för respektive NIC-resurser med hjälp av [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs kan du använda kommandot [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en standard belastningsutjämnare, konfigurerat trafik regler för både inkommande belastningsutjämnare, konfigurerade och hälso avsökningar för de virtuella datorerna i backend-poolen. Om du vill veta mer om Azure Load Balancer kan du fortsätta till självstudierna för Azure Load Balancer.

> [!div class="nextstepaction"]
> [Självstudier om Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
