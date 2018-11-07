---
title: Med flera virtuella IP-adresser för en molntjänst
description: Översikt över multiVIP och hur du anger flera VIP för en molntjänst
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 03ecb44f3d561ff720939d0605d342b299194ef2
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218805"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Konfigurera flera VIP för en molntjänst

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Du kan komma åt Azure-molntjänster via det offentliga Internet med hjälp av en IP-adress som tillhandahålls av Azure. Den här offentliga IP-adressen kallas för en VIP (virtuell IP-adress) eftersom den är länkad till Azure-belastningsutjämnaren och inte den virtuella datorn (VM)-instanser i Molntjänsten. Du kan komma åt alla VM-instans i en molntjänst med hjälp av en enskild VIP.

Det finns dock scenarier där du kan behöva mer än en VIP som en post som pekar på samma molntjänst. Exempelvis kan din molntjänst kan vara värd för flera webbplatser som kräver SSL-anslutning med standardporten 443, som är värd för varje plats för en annan kund eller klient. I det här scenariot behöver du en annan offentlig IP-adress för varje webbplats. Diagrammet nedan illustrerar en typisk flera innehavare webbvärdtjänster har behov av flera SSL-certifikat på samma offentlig port.

![Flera VIP SSL-scenario](./media/load-balancer-multivip/Figure1.png)

I exemplet ovan använder samma offentlig port (443) för alla virtuella IP-adresser och trafik dirigeras till en eller flera belastningsutjämnade virtuella datorer på en unik privat port för den interna IP-adressen för Molntjänsten som är värd för alla webbplatser.

> [!NOTE]
> En annan situation som att kräva att flera virtuella IP-adresser är värd för flera SQL AlwaysOn availability-grupplyssnare i samma uppsättning virtuella datorer.

Virtuella IP-adresser är dynamiska som standard, vilket innebär att själva IP-adressen som tilldelas till Molntjänsten kan ändras över tid. Om du vill förhindra detta kan du reservera en VIP för din tjänst. Läs mer om reserverade virtuella IP-adresser i [reserverade offentliga IP-Adressen](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Se [priser för IP-adress](https://azure.microsoft.com/pricing/details/ip-addresses/) information om priser på virtuella IP-adresser och reserverade IP-adresser.

Du kan använda PowerShell för att kontrollera VIP-adresser som används av cloud services, samt lägga till och ta bort VIP-adresser, associera en VIP-adress till en slutpunkt och konfigurera belastningsutjämning på en specifik VIP.

## <a name="limitations"></a>Begränsningar

Just nu är med flera VIP-funktionerna begränsade till följande scenarier:

* **IaaS endast**. Du kan bara aktivera flera VIP för molntjänster som innehåller virtuella datorer. Du kan inte använda flera Virtuella i PaaS-scenarier med rollinstanser.
* **Endast PowerShell**. Du kan bara hantera flera Virtuella med hjälp av PowerShell.

Dessa begränsningar är tillfälliga och kan ändras när som helst. Se till att gå tillbaka till den här sidan för att verifiera framtida ändringar.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Hur du lägger till en VIP-adress till en molntjänst
Om du vill lägga till en VIP till din tjänst, kör du följande PowerShell-kommando:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Det här kommandot visar ett resultat som liknar följande exempel:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Ta bort en VIP från en tjänst i molnet
Om du vill ta bort VIP-Adressen läggs till din tjänst i exemplet ovan, kör du följande PowerShell-kommando:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Du kan bara ta bort en VIP om den har inga slutpunkter som är associerade med den.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Hur du hämtar VIP-information från en tjänst i molnet
Kör följande PowerShell-skript för att hämta de virtuella IP-adresser som är associerade med en molnbaserad tjänst:

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

I det här exemplet Molntjänsten har 3 virtuella IP-adresser:

* **Vip1** är standard-VIP du vet att eftersom värdet för IsDnsProgrammedName har angetts till true.
* **Vip2** och **Vip3** används inte eftersom de inte har några IP-adresser. De ska endast användas om du associerar en slutpunkt för att VIP-Adressen.

> [!NOTE]
> Din prenumeration debiteras bara för extra virtuella IP-adresser när de är kopplade till en slutpunkt. Mer information om priser finns i [priser för IP-adress](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Så här associerar du en VIP-adress till en slutpunkt

Om du vill associera en VIP på en tjänst i molnet till en slutpunkt, kör du följande PowerShell-kommando:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Kommandot skapar en slutpunkt som är länkad till den VIP som kallas *Vip2* på port *80*, och länkar till den virtuella datorn med namnet *myVM1* i en molntjänst med namnet *myService* med *TCP* på port *8080*.

Kontrollera konfigurationen med följande PowerShell-kommando:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Utdata ser ut ungefär så här:

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

Du kan associera en enskild VIP med flera virtuella datorer för belastningsutjämning syften. Exempel: du har en molntjänst med namnet *myService*, och två virtuella datorer med namnet *myVM1* och *myVM2*. Och Molntjänsten har flera virtuella IP-adresser, en av dem med namnet *Vip2*. Om du vill se till att all trafik till port *81* på *Vip2* balanseras mellan *myVM1* och *myVM2* på port *8181* , kör följande PowerShell-skript:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

Du kan också uppdatera belastningsutjämnaren om du vill använda en annan VIP. Om du kör PowerShell-kommandot nedan kommer du till exempel ändra belastningsutjämning som ska användas av en VIP-adress med namnet Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Nästa steg

[Logganalys för Azure-belastningsutjämning](load-balancer-monitor-log.md)

[Internet riktade översikt över standardbelastningsutjämnare](load-balancer-internet-overview.md)

[Kom igång med belastningsutjämnare mot Internet](load-balancer-get-started-internet-arm-ps.md)

[Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md)

[Reserverade IP-REST API: er](https://msdn.microsoft.com/library/azure/dn722420.aspx)
