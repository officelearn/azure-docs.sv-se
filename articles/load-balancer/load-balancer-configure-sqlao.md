---
title: "Konfigurera belastningsutjämning för SQLServer alltid på | Microsoft Docs"
description: "Konfigurera belastningsutjämnare för att arbeta med SQL Server alltid aktiverad och lär dig hur du använder PowerShell för att skapa en belastningsutjämnare för SQL Server-implementering"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Konfigurera en belastningsutjämnare för SQL Server alltid aktiverad

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server Always On-Tillgänglighetsgrupper kan nu köra med en intern belastningsutjämnare. En tillgänglighetsgrupp är SQL Server bästa lösning för hög tillgänglighet och katastrofåterställning. Tillgänglighetsgruppslyssnaren ger klientprogram sömlöst ansluta till den primära repliken, oavsett antal repliker i konfigurationen.

Lyssnare (DNS) namn är mappad till en belastningsutjämnad IP-adress. Azure belastningsutjämnare dirigerar inkommande trafik till bara den primära servern i uppsättningen.

Du kan använda interna belastningsutjämnare stöd för SQL Server alltid aktiverad (lyssnare) slutpunkter. Nu har du kontroll över tillgängligheten för lyssnaren. Du kan välja belastningsutjämnad IP-adress från ett specifikt undernät i det virtuella nätverket.

Med hjälp av en intern belastningsutjämnare på lyssnaren slutpunkten för SQL Server (till exempel Server = tcp:ListenerName, 1433; Database = DatabaseName) endast är tillgänglig för:

* Tjänster och virtuella datorer i samma virtuella nätverk.
* Tjänster och virtuella datorer från anslutna lokalt nätverk.
* Tjänster och virtuella datorer från virtuella nätverk.

![Intern belastningsutjämnare SQL Server alltid aktiverad](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Lägg till en intern belastningsutjämnare till tjänsten

1. I exemplet nedan kan du konfigurera ett virtuellt nätverk som innehåller ett undernät som kallas Undernät-1:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Lägg till belastningsutjämnade slutpunkter för en intern belastningsutjämnare på varje virtuell dator.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    I föregående exempel har två virtuella datorer kallas ”sqlsvc1” och ”sqlsvc2” som körs i Molntjänsten ”Sqlsvc”. När du har skapat den interna belastningsutjämnaren med den `DirectServerReturn` växlar kan du lägga till belastningsutjämnade slutpunkter i den interna belastningsutjämnaren. Belastningsutjämnade slutpunkter kan SQL Server för att konfigurera lyssnare för tillgänglighetsgrupperna.

Mer information om SQL Server alltid aktiverad finns [konfigurera en intern belastningsutjämnare för en tillgänglighetsgrupp alltid på i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Se även
* [Kom igång med att konfigurera en offentlig belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)
* [Komma igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)
* [Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)
* [Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
