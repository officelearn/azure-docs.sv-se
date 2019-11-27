---
title: Konfigurera belastnings utjämning och utgående regler med hjälp av Azure PowerShell
titleSuffix: Azure Load Balancer
description: Den här artikeln visar hur du konfigurerar belastnings utjämning och utgående regler i Standard Load Balancer genom att använda Azure PowerShell.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: 5fd68f4559420ca688b3f4d6f6d66ee52db5191e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225431"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-azure-powershell"></a>Konfigurera belastnings utjämning och utgående regler i Standard Load Balancer med Azure PowerShell

Den här artikeln visar hur du konfigurerar utgående regler i Standard Load Balancer med hjälp av Azure PowerShell.  

När du är klar med den här artikelns scenario innehåller belastnings Utjämnings resursen två klient delar och deras associerade regler. Du har en klient del för inkommande trafik och en annan klient del för utgående trafik.  

Varje klient del refererar till en offentlig IP-adress. I det här scenariot skiljer sig den offentliga IP-adressen för inkommande trafik från adressen för utgående trafik.   Belastnings Utjämnings regeln tillhandahåller endast inkommande belastnings utjämning. Regeln för utgående trafik styr utgående Network Address Translation (NAT) för den virtuella datorn.  

Scenariot använder två server dels pooler: en för inkommande trafik och en för utgående trafik. Dessa pooler illustrerar kapaciteten och ger flexibilitet för scenariot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-azure-account"></a>Anslut till ditt Azure-konto
Logga in på din Azure-prenumeration med hjälp av kommandot [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0). Följ sedan anvisningarna på skärmen.
    
```azurepowershell-interactive
Connect-AzAccount
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resurs grupp med hjälp av [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0). En Azure-resurs grupp är en logisk behållare där Azure-resurser distribueras. Resurserna hanteras sedan från gruppen.

I följande exempel skapas en resurs grupp med namnet *myresourcegroupoutbound* på *eastus2* -platsen:

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroupoutbound -Location eastus
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk
Skapa ett virtuellt nätverk med namnet *myvnetoutbound*. Namnge dess undernät *mysubnetoutbound*. Placera den i *myresourcegroupoutbound* med [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=azps-2.6.0) och [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=azps-2.6.0).

```azurepowershell-interactive
$subnet = New-AzVirtualNetworkSubnetConfig -Name mysubnetoutbound -AddressPrefix "192.168.0.0/24"

New-AzVirtualNetwork -Name myvnetoutbound -ResourceGroupName myresourcegroupoutbound -Location eastus -AddressPrefix "192.168.0.0/16" -Subnet $subnet
```

## <a name="create-an-inbound-public-ip-address"></a>Skapa en offentlig IP-adress för inkommande trafik 

För att få åtkomst till din webbapp på Internet behöver du en offentlig IP-adress för belastningsutjämnaren. Standard Load Balancer stöder endast offentliga IP-adresser som är standard. 

Använd [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0) för att skapa en offentlig standard-IP-adress med namnet *mypublicipinbound* i *myresourcegroupoutbound*.

```azurepowershell-interactive
$pubIPin = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipinbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-outbound-public-ip-address"></a>Skapa en utgående offentlig IP-adress 

Skapa en standard-IP-adress för belastningsutjämnarens utgående konfiguration för klient delen med hjälp av [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=azps-2.6.0).

```azurepowershell-interactive
$pubIPout = New-AzPublicIpAddress -ResourceGroupName myresourcegroupoutbound -Name mypublicipoutbound -AllocationMethod Static -Sku Standard -Location eastus
```

## <a name="create-an-azure-load-balancer"></a>skapa en Azure Load Balancer

I det här avsnittet beskrivs hur du skapar och konfigurerar följande komponenter i belastningsutjämnaren:
  - En frontend-IP-adress som tar emot inkommande nätverks trafik på belastningsutjämnaren
  - En backend-pool där klient delens IP skickar den belastningsutjämnade nätverks trafiken
  - En backend-pool för utgående anslutning
  - En hälso avsökning som fastställer hälso tillståndet för backend VM-instanser
  - En regel för inkommande belastnings utjämning som definierar hur trafiken distribueras till de virtuella datorerna
  - En regel för utgående belastningsutjämnare som definierar hur trafiken distribueras från de virtuella datorerna

### <a name="create-an-inbound-front-end-ip"></a>Skapa en inkommande IP-adress för klient delen
Skapa den inkommande klient delens IP-konfiguration för belastningsutjämnaren med hjälp av [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Belastningsutjämnaren ska inkludera en inkommande klient dels-IP-konfiguration med namnet *myfrontendinbound*. Koppla den här konfigurationen till den offentliga IP- *mypublicipinbound*.

```azurepowershell-interactive
$frontendIPin = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendinbound" -PublicIpAddress $pubIPin
```
### <a name="create-an-outbound-front-end-ip"></a>Skapa en utgående IP-adress för klient delen
Skapa den utgående klient delens IP-konfiguration för belastningsutjämnaren med hjälp av [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig?view=azps-2.6.0). Den här belastningsutjämnaren ska innehålla en utgående IP-konfiguration för klient delen med namnet *myfrontendoutbound*. Koppla den här konfigurationen till den offentliga IP- *mypublicipoutbound*.

```azurepowershell-interactive
$frontendIPout = New-AzLoadBalancerFrontendIPConfig -Name "myfrontendoutbound" -PublicIpAddress $pubIPout
```
### <a name="create-an-inbound-back-end-pool"></a>Skapa en inkommande backend-pool
Skapa server delens inkommande pool för belastningsutjämnaren med hjälp av [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Namnge poolen *bepoolinbound*.

```azurepowershell-interactive
$bepoolin = New-AzLoadBalancerBackendAddressPoolConfig -Name bepoolinbound
``` 

### <a name="create-an-outbound-back-end-pool"></a>Skapa en utgående backend-pool
Använd följande kommando för att skapa en annan backend-adresspool för att definiera utgående anslutning för en pool med virtuella datorer genom att använda [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig?view=azps-2.6.0). Namnge poolen *bepooloutbound*. 

Genom att skapa en separat utgående pool ger du maximal flexibilitet. Men du kan utelämna det här steget och endast använda inkommande *bepoolinbound* om du föredrar det.  

```azurepowershell-interactive
$bepoolout = New-AzLoadBalancerBackendAddressPoolConfig -Name bepooloutbound
``` 

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökning

En hälso avsökning kontrollerar alla virtuella dator instanser för att se till att de kan skicka nätverks trafik. Den virtuella dator instans som inte klarar avsöknings kontrollerna tas bort från belastningsutjämnaren tills den går tillbaka online och en avsöknings kontroll avgör att den är felfri. 

Du övervakar hälso tillståndet för de virtuella datorerna genom att skapa en hälso avsökning med hjälp av [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig?view=azps-2.6.0). 

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name http -Protocol "http" -Port 80 -IntervalInSeconds 15 -ProbeCount 2 -RequestPath /
```
### <a name="create-a-load-balancer-rule"></a>Skapa en belastnings Utjämnings regel

En belastnings Utjämnings regel definierar klient delens IP-konfiguration för inkommande trafik och backend-poolen för att ta emot trafiken. Den definierar också den nödvändiga käll-och mål porten. 

Skapa en belastnings Utjämnings regel med namnet *myinboundlbrule* med hjälp av [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig?view=azps-2.6.0). Den här regeln lyssnar på port 80 i klient delens *myfrontendinbound*. Den kommer också att använda port 80 för att skicka belastningsutjämnad nätverks trafik till backend-adresspoolen *bepoolinbound*. 

```azurepowershell-interactive
$inboundRule = New-AzLoadBalancerRuleConfig -Name inboundlbrule -FrontendIPConfiguration $frontendIPin -BackendAddressPool $bepoolin -Probe $probe -Protocol "Tcp" -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -DisableOutboundSNAT
```

>[!NOTE]
>Den här belastnings Utjämnings regeln inaktiverar automatisk utgående säker NAT (SNAT) på grund av parametern **-DisableOutboundSNAT** . Utgående NAT tillhandahålls endast av regeln för utgående trafik.

### <a name="create-an-outbound-rule"></a>Skapa en regel för utgående trafik

En utgående regel definierar den offentliga IP-adressen, som representeras av klient delens *myfrontendoutbound*. Den här klient delen kommer att användas för all utgående NAT-trafik samt den backend-pool som regeln gäller.  

Använd följande kommando för att skapa en utgående regel *myoutboundrule* för utgående nätverks översättning av alla virtuella datorer (i NIC IP-konfigurationer) i backend-poolen för *pooler* .  Kommandot ändrar den utgående tids gränsen för inaktivitet från 4 till 15 minuter. Den allokerar 10 000 SNAT-portar i stället för 1 024. Mer information finns i [New-AzLoadBalancerOutboundRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalanceroutboundruleconfig?view=azps-2.7.0).

```azurepowershell-interactive
 $outboundRule = New-AzLoadBalancerOutBoundRuleConfig -Name outboundrule -FrontendIPConfiguration $frontendIPout -BackendAddressPool $bepoolout -Protocol All -IdleTimeoutInMinutes 15 -AllocatedOutboundPort 10000
```
Om du inte vill använda en separat utgående pool kan du ändra argumentet för adresspoolen i föregående kommando för att ange *$bepoolin* i stället.  Vi rekommenderar att du använder separata pooler för att göra den resulterande konfigurationen flexibel och läsbar.

### <a name="create-a-load-balancer"></a>Skapa en lastbalanserare

Använd följande kommando för att skapa en belastningsutjämnare för den inkommande IP-adressen med hjälp av [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer?view=azps-2.6.0). Ge belastningsutjämnaren *lb*. Den bör innehålla en inkommande IP-konfiguration för klient delen. Dess backend- *bepoolinbound* bör associeras med den offentliga IP- *mypublicipinbound* som du skapade i föregående steg.

```azurepowershell-interactive
New-AzLoadBalancer -Name lb -Sku Standard -ResourceGroupName myresourcegroupoutbound -Location eastus -FrontendIpConfiguration $frontendIPin,$frontendIPout -BackendAddressPool $bepoolin,$bepoolout -Probe $probe -LoadBalancingRule $inboundrule -OutboundRule $outboundrule 
```

Nu kan du fortsätta att lägga till dina virtuella datorer i både *bepoolinbound* -och *bepooloutbound* -backend-pooler genom att uppdatera IP-konfigurationen för respektive NIC-resurser. Uppdatera resurs konfigurationen med hjälp av [Add-AzNetworkInterfaceIpConfig](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver resurs gruppen, belastningsutjämnaren och relaterade resurser kan du ta bort dem med hjälp av [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.7.0).

```azurepowershell-interactive 
  Remove-AzResourceGroup -Name myresourcegroupoutbound
```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du skapat en standard belastningsutjämnare, konfigurerat trafik regler för både inkommande och utgående belastnings utjämning och konfigurerade en hälso avsökning för de virtuella datorerna i backend-poolen. 

Om du vill veta mer kan du fortsätta till [självstudierna för Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md).
