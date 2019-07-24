---
title: Flera VIP-adresser för en moln tjänst
titlesuffix: Azure Load Balancer
description: Översikt över multiVIP och hur du ställer in flera virtuella IP-adresser i en moln tjänst
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274696"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurera flera VIP för en moln tjänst

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Du kan komma åt Azure Cloud Services via det offentliga Internet genom att använda en IP-adress som tillhandahålls av Azure. Den här offentliga IP-adressen kallas för en VIP (virtuell IP) eftersom den är länkad till Azure Load Balancer och inte de virtuella dator instanserna (VM) i moln tjänsten. Du kan komma åt en virtuell dator instans i en moln tjänst genom att använda en enda VIP.

Det finns dock scenarier där du kan behöva mer än en VIP som en start punkt till samma moln tjänst. Till exempel kan moln tjänsten vara värd för flera webbplatser som kräver SSL-anslutning med standard porten 443, eftersom varje plats är värd för en annan kund eller klient. I det här scenariot måste du ha en annan offentlig IP-adress för varje webbplats. Diagrammet nedan illustrerar en typisk webb värd för flera innehavare med ett behov av flera SSL-certifikat på samma offentliga port.

![Multi VIP SSL-scenario](./media/load-balancer-multivip/Figure1.png)

I exemplet ovan använder alla VIP samma offentliga port (443) och trafik omdirigeras till en eller flera belastningsutjämnade virtuella datorer på en unik privat port för den interna IP-adressen för moln tjänsten som är värd för alla webbplatser.

> [!NOTE]
> En annan situation som kräver användning av flera VIP: er är värd för flera SQL AlwaysOn tillgänglighets grupps lyssnare på samma uppsättning Virtual Machines.

VIP-adresser är dynamiska som standard, vilket innebär att den faktiska IP-adress som tilldelats moln tjänsten kan ändras med tiden. För att förhindra att det sker kan du reservera en VIP för din tjänst. Mer information om reserverade virtuella [IP-adresser finns i reserverade offentliga IP-adresser](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Information om priser för virtuella [IP-](https://azure.microsoft.com/pricing/details/ip-addresses/) adresser och reserverade IP-adresser finns i priser för IP-adress.

Du kan använda PowerShell för att kontrol lera de virtuella IP-adresser som används av dina moln tjänster, samt lägga till och ta bort VIP, associera en VIP till en slut punkt och konfigurera belastnings utjämning på en särskild VIP.

## <a name="limitations"></a>Begränsningar

För närvarande är flera VIP-funktioner begränsade till följande scenarier:

* **Endast IaaS**. Du kan bara aktivera flera VIP för moln tjänster som innehåller virtuella datorer. Du kan inte använda flera VIP i PaaS-scenarier med roll instanser.
* **Endast PowerShell**. Du kan bara hantera flera VIP med hjälp av PowerShell.

Dessa begränsningar är tillfälliga och kan ändras när som helst. Se till att gå tillbaka till den här sidan för att verifiera framtida ändringar.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Så här lägger du till en VIP i en moln tjänst
Kör följande PowerShell-kommando om du vill lägga till en VIP till din tjänst:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Det här kommandot visar ett resultat som liknar följande exempel:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Så här tar du bort en VIP från en moln tjänst
Om du vill ta bort VIP-tillägget som har lagts till i din tjänst i exemplet ovan kör du följande PowerShell-kommando:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Du kan bara ta bort en VIP om den inte har några kopplade slut punkter.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Så här hämtar du VIP-information från en moln tjänst
Kör följande PowerShell-skript för att hämta de virtuella IP-adresser som är kopplade till en moln tjänst:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Skriptet visar ett resultat som liknar följande exempel:

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

I det här exemplet har moln tjänsten 3 VIP: er:

* **Vip1** är standard-VIP, du vet att eftersom värdet för IsDnsProgrammedName har angetts till true.
* **Vip2** och **Vip3** används inte eftersom de inte har några IP-adresser. De kommer bara att användas om du associerar en slut punkt till VIP.

> [!NOTE]
> Din prenumeration debiteras bara för extra VIP när de är kopplade till en slut punkt. Mer information om priser finns i pris information för [IP-adresser](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Så här associerar du en VIP till en slut punkt

Om du vill associera en VIP-tjänst i en moln tjänst till en slut punkt kör du följande PowerShell-kommando:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Kommandot skapar en slut punkt som är länkad till den VIP som heter *Vip2* på port *80*och länkar den till den virtuella datorn med namnet *myVM1* i en moln tjänst med namnet unservice med *TCP* på port *8080*.

Verifiera konfigurationen genom att köra följande PowerShell-kommando:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Utdata ser ut ungefär som i följande exempel:

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

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Så här aktiverar du belastnings utjämning på en särskild VIP

Du kan associera en enda VIP med flera virtuella datorer för belastnings utjämning. Du kan till exempel ha en moln tjänst med namnet unservice och två virtuella datorer med namnet *myVM1* och *myVM2*. Och moln tjänsten har flera virtuella IP-adresser, varav de heter *Vip2*. Om du vill se till att all trafik till port *81* på *Vip2* är bal anse rad mellan *myVM1* och *MyVM2* på port *8181*kör du följande PowerShell-skript:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Du kan också uppdatera belastningsutjämnaren för att använda en annan VIP. Om du till exempel kör PowerShell-kommandot nedan, kommer du att ändra belastnings Utjämnings uppsättningen så att den använder en VIP med namnet Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Nästa steg

[Azure Monitor loggar för Azure-belastnings utjämning](load-balancer-monitor-log.md)

[Översikt över belastningsutjämnare mot Internet](load-balancer-internet-overview.md)

[Kom igång med belastningsutjämnare på Internet](load-balancer-get-started-internet-arm-ps.md)

[Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)

[Reserverad IP REST-API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx)
