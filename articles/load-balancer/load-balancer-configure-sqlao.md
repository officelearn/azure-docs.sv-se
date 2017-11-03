---
title: "Konfigurera belastningsutjämning för SQL alltid på | Microsoft Docs"
description: "Konfigurera belastningsutjämning ska fungera med SQL alltid på och hur man utnyttjar powershell för att skapa belastningsutjämnaren för SQL-implementering"
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
ms.openlocfilehash: 3ebbf1c4009d89b1f18b2ff8ff5dd243c456dff8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-load-balancer-for-sql-always-on"></a>Konfigurera belastningsutjämning för SQL alltid på

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

SQL Server AlwaysOn-Tillgänglighetsgrupper kan nu köra med ILB. Tillgänglighetsgruppen är SQL Server bästa lösning för hög tillgänglighet och haveriberedskap. Tillgänglighetsgruppslyssnaren ger klientprogram sömlöst ansluta till den primära repliken, oavsett antal repliker i konfigurationen.

Lyssnare (DNS) namn är mappad till en belastningsutjämnad IP-adress och belastningsutjämnaren för Azures dirigerar inkommande trafik till bara den primära servern i uppsättningen.

Du kan använda ILB stöd för SQL Server AlwaysOn (lyssnare) slutpunkter. Du har kontroll över tillgängligheten för lyssnaren nu och kan välja belastningsutjämnad IP-adress från ett specifikt undernät i ditt virtuella nätverk (VNet).

Med hjälp av ILB på lyssnaren slutpunkten för SQL server (t.ex. Server = tcp:ListenerName, 1433; Database = DatabaseName) endast är tillgänglig för:

* Tjänster och virtuella datorer i samma virtuella nätverk
* Tjänster och virtuella datorer från anslutna lokalt nätverk
* Tjänster och virtuella datorer från sammankopplade Vnet

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Bild 1 – SQL AlwaysOn har konfigurerats med Internetriktade belastningsutjämnare

## <a name="add-internal-load-balancer-to-the-service"></a>Lägga till interna belastningsutjämnare till tjänsten

1. I följande exempel ska vi konfigurera ett virtuellt nätverk som innehåller ett undernät som kallas Undernät-1:

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Lägga till belastningsutjämnade slutpunkter för ILB på varje virtuell dator

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    I exemplet ovan kan du har 2 VM kallas ”sqlsvc1” och ”sqlsvc2” som körs i molnet tjänsten ”Sqlsvc”. När du har skapat ILB med `DirectServerReturn` växel, du lägger till belastningsutjämnade slutpunkter ILB så att SQL konfigurera lyssnare för tillgänglighetsgrupperna.

Mer information om SQL AlwaysOn finns [konfigurera en intern belastningsutjämnare för en AlwaysOn-tillgänglighetsgrupp i Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

## <a name="see-also"></a>Se även
[Kom igång med att konfigurera en Internetuppkopplad belastningsutjämnare](load-balancer-get-started-internet-arm-ps.md)

[Kom igång med att konfigurera en intern belastningsutjämnare](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurera ett distributionsläge för belastningsutjämnare](load-balancer-distribution-mode.md)

[Konfigurera timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md)
