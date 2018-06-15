---
title: 'Mutiple VIP: er för en tjänst i molnet'
description: 'Översikt över multiVIP och hur du anger flera VIP: er för en tjänst i molnet'
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: b6b7b0b2d7a7f33facaf72bbd2d7937364770673
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855203"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurera flera VIP: er för en tjänst i molnet

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Du kan komma åt Azure-molntjänster via det offentliga Internet med hjälp av en IP-adress som tillhandahålls av Azure. Den här offentliga IP-adressen kallas för en VIP (virtuell IP) eftersom den är kopplad till Azure belastningsutjämnare och inte den virtuella datorn (VM) instanser i Molntjänsten. Du kan komma åt alla VM-instans i en molntjänst med hjälp av en enskild VIP.

Det finns emellertid scenarier där du kanske behöver flera VIP som en post som pekar på samma tjänst i molnet. Molntjänsten kan exempelvis vara värd för flera webbplatser som kräver SSL-anslutning med standardporten 443, eftersom varje plats är värd för en annan kund eller klient. I det här scenariot behöver du en annan offentlig Internetriktade IP-adress för varje webbplats. Diagrammet nedan illustrerar en typisk flera innehavare webbvärd med behov för flera SSL-certifikat på samma offentlig port.

![Scenario med flera VIP SSL](./media/load-balancer-multivip/Figure1.png)

I exemplet ovan alla VIP: er använda samma offentlig port (443) och trafiken dirigeras till en eller flera belastningsutjämnade virtuella datorer på en unik privat port för interna IP-adressen för den molntjänst som är värd för alla webbplatser.

> [!NOTE]
> En annan situation att använda flera VIP: er är värd för flera SQL AlwaysOn availability tillgänglighetsgruppslyssnarnas på samma uppsättning virtuella datorer.

VIP-adresser är dynamiska som standard, vilket innebär att den faktiska IP-adress som tilldelats Molntjänsten kan förändras över tid. Om du vill förhindra detta att reservera en VIP för din tjänst. Mer information om reserverade virtuella IP-adresser finns [reserverade offentliga IP-Adressen](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Se [IP-adress priser](https://azure.microsoft.com/pricing/details/ip-addresses/) information om priser på virtuella IP-adresser och reserverade IP-adresser.

Du kan använda PowerShell för att kontrollera VIP som används av dina molntjänster samt lägga till och ta bort VIP-adresser, associera en VIP till en slutpunkt och konfigurera belastningsutjämning för en specifik VIP.

## <a name="limitations"></a>Begränsningar

För tillfället är Multi-VIP-funktionerna begränsade till följande scenarier:

* **Endast IaaS**. Du kan bara aktivera Multi-VIP för molntjänster som innehåller virtuella datorer. Du kan använda flera VIP i PaaS scenarier med rollinstanser.
* **PowerShell endast**. Du kan endast hantera flera VIP med hjälp av PowerShell.

Dessa begränsningar är tillfälliga och kan ändras när som helst. Se till att ångra den här sidan om du vill kontrollera framtida ändringar.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Hur du lägger till en VIP till en molntjänst
Kör följande PowerShell-kommando för att lägga till en VIP till din tjänst:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Detta kommando visar ett resultat som liknar följande exempel:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Ta bort en VIP från en molnbaserad tjänst
Kör följande PowerShell-kommando för att ta bort VIP-Adressen till din tjänst i exemplet ovan:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Du kan bara ta bort en VIP om det har inga slutpunkter som är kopplade till den.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Hur du hämtar VIP information från en molnbaserad tjänst
Kör följande PowerShell-skript för att hämta VIP som är associerade med en tjänst i molnet:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skriptet visar resultatet liknar följande exempel:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

I det här exemplet har Molntjänsten 3 VIP:

* **Vip1** är standard-VIP du vet att eftersom värdet för IsDnsProgrammedName har angetts till true.
* **Vip2** och **Vip3** används inte eftersom de inte har några IP-adresser. De ska endast användas om du kopplar en slutpunkt till VIP.

> [!NOTE]
> Din prenumeration kommer bara att debiteras för extra VIP: er när de är kopplade till en slutpunkt. Mer information om priser finns [priser för IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Så här associerar du en VIP till en slutpunkt

Kör följande PowerShell-kommando om du vill associera en VIP på en tjänst i molnet till en slutpunkt:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Kommandot skapar en slutpunkt som är kopplad till VIP kallas *Vip2* på port *80*, samt länkar till den virtuella datorn med namnet *myVM1* i en molntjänst med namnet *myService* med *TCP* på port *8080*.

Kör följande PowerShell-kommando för att verifiera konfigurationen:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Utdata liknar följande exempel:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Så här aktiverar du belastningsutjämning på en specifik VIP

Du kan koppla en enskild VIP till flera virtuella datorer för belastningsutjämning syften. Du till exempel har en molntjänst med namnet *myService*, och två virtuella datorer med namnet *myVM1* och *myVM2*. Och Molntjänsten har flera VIP: er, en av dem med namnet *Vip2*. Om du vill se till att all trafik till port *81* på *Vip2* balanseras mellan *myVM1* och *myVM2* på port *8181*, kör följande PowerShell-skript:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Du kan också uppdatera din belastningsutjämnare för att använda en annan VIP. Om du kör PowerShell-kommandot nedan ska du ändra belastningsutjämning som ska användas av en VIP med namnet Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Nästa steg

[Logganalys för belastningsutjämning i Azure](load-balancer-monitor-log.md)

[Internet Internetriktade belastningen översikt över belastningsutjämnare](load-balancer-internet-overview.md)

[Komma igång med belastningsutjämnaren mot Internet](load-balancer-get-started-internet-arm-ps.md)

[Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)

[Reserverad IP REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx)
