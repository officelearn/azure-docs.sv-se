---
title: Skapa en intern Azure-belastningsutjämnare – klassiska PowerShell | Microsoft Docs
description: Lär dig hur du skapar en intern belastningsutjämnare med hjälp av PowerShell i den klassiska distributionsmodellen
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 3be93168-3787-45a5-a194-9124fe386493
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 8b896705d90b51c056172c285a00dabeed54ebf2
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Komma igång med att skapa en intern belastningsutjämnare (klassisk) med hjälp av PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Molntjänster](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../azure-resource-manager/resource-manager-deployment-model.md).  Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Skapa en intern belastningsutjämningsuppsättning för virtuella datorer

Följ dessa steg för att skapa en intern belastningsutjämningsuppsättning och de servrar som ska skicka sin trafik till den:

1. Skapa en instans för intern belastningsutjämning som ska vara slutpunkten för inkommande trafik som ska belastningsutjämnas mellan servrarna i en belastningsutjämnad uppsättning.
2. Lägg till slutpunkter som motsvarar de virtuella datorerna som ska ta emot den inkommande trafiken.
3. Konfigurera servrarna som ska skicka trafiken som ska belastningsutjämnas så att de skickar trafiken till den virtuella IP-adressen för instansen för intern belastningsutjämning.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Steg 1: Skapa en instans för intern belastningsutjämning

För en befintlig molntjänst eller en molntjänst som distribuerats i ett regionalt virtuellt nätverk kan du skapa en instans för intern belastningsutjämning med hjälp av följande Windows PowerShell-kommandon:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Observera att den här användningen av Windows PowerShell-cmdleten [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) använder parameteruppsättningen DefaultProbe. Mer information om fler parameteruppsättningar finns i [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Steg 2: Lägga till slutpunkter i instansen för intern belastningsutjämning

Här är ett exempel:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Steg 3: Konfigurera servrarna så att de skickar trafiken till slutpunkten för den nya interna belastningsutjämningen

Du måste konfigurera servrarna vars trafik ska belastningsutjämnas så att de använder den nya IP-adressen (VIP-adressen) för instansen för intern belastningsutjämning. Det här är den adress som instansen för intern belastningsutjämning lyssnar på. I de flesta fall behöver du bara lägga till eller ändra en DNS-post för VIP-adressen för instansen för intern belastningsutjämning.

Om du angav IP-adressen under genereringen av instansen för intern belastningsutjämning har du redan VIP-adressen. Annars kan du hämta VIP-adressen med följande kommandon:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

När du använder dessa kommandon fyller du i värdena och tar bort < och >. Här är ett exempel:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Notera IP-adressen på skärmen som returneras när du kör kommandot Get-AzureInternalLoadBalancer och gör nödvändiga ändringar i server- eller DNS-posterna så att trafiken skickas till VIP-adressen.

> [!NOTE]
> Microsoft Azure-plattformen använder en statisk, offentligt dirigerbar IPv4-adress för en rad olika administrativa scenarier. IP-adressen är 168.63.129.16. Den här IP-adressen får inte blockeras av eventuella brandväggar eftersom det kan orsaka oväntade resultat.
> Vid belastningsutjämning i Azure används den här IP-adressen av övervakningsavsökningar från belastningsutjämnaren för att fastställa hälsotillståndet för virtuella datorer i en belastningsutjämnad uppsättning. Om en nätverkssäkerhetsgrupp används för att begränsa trafik till virtuella datorer i Azure i en internt belastningsutjämnad uppsättning eller om den tillämpas på ett virtuellt nätverksundernät krävs en nätverkssäkerhetsregel som tillåter trafik från 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Exempel på intern belastningsutjämning

Följande avsnitt innehåller två exempelkonfigurationer och vägleder dig genom hela processen när du skapar en belastningsutjämnad uppsättning.

### <a name="an-internet-facing-multi-tier-application"></a>Ett Internetuppkopplat flernivåprogram

Anta att du vill tillhandahålla en belastningsutjämnad databastjänst för en uppsättning Internetuppkopplade webbservrar. Båda uppsättningarna med servrar finns i samma Azure-molntjänst. Webbservertrafik till TCP-port 1433 måste distribueras mellan två virtuella datorer på databasnivån. Bild 1 visar konfigurationen.

![Intern belastningsutjämnad uppsättning för databasnivån](./media/load-balancer-internal-getstarted/IC736321.png)

Konfigurationen består av följande:

* Den befintliga molntjänsten som är värd för de virtuella datorerna har namnet mytestcloud.
* De två befintliga databasservrarna heter DB1 och DB2.
* Webbservrar på webbnivån ansluter till databasservrarna på databasnivån med hjälp av den privata IP-adressen. Ett annat alternativ är att använda din egen DNS för det virtuella nätverket och att manuellt registrera en A-post för den interna belastningsutjämningsuppsättningen.

Följande kommandon konfigurerar en ny instans för intern belastningsutjämning med namnet **ILBset** och lägger till slutpunkter till de virtuella datorerna som motsvarar de två databasservrarna:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Ta bort en konfiguration för intern belastningsutjämning

Om du vill ta bort en virtuell dator som slutpunkt från en instans av en intern belastningsutjämnare använder du följande kommandon:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

När du använder dessa kommandon fyller du i värdena och tar bort < och >.

Här är ett exempel:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Använd följande kommandon om du vill ta bort en instans av en intern belastningsutjämnare från en molntjänst:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

När du använder dessa kommandon fyller du i värdet och tar bort < och >.

Här är ett exempel:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Mer information om cmdlets för interna belastningsutjämnare

Om du vill ha mer information om cmdlets för intern belastningsutjämning kör du följande kommandon i Windows PowerShell-kommandotolken:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Nästa steg

[Konfigurera ett distributionsläge för belastningsutjämnare med hjälp av käll-IP-tilldelning](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)

