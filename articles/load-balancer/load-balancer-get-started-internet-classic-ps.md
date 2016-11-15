---
title: "Komma igång med att skapa en Internetuppkopplad belastningsutjämnare i klassiskt läge med hjälp av PowerShell | Microsoft Docs"
description: "Lär dig hur du skapar en Internetuppkopplad belastningsutjämnare i klassiskt läge med hjälp av PowerShell"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7344f2c3eeb7d52f8bc60e564d66b2cc51f10f75

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Komma igång med att skapa en Internetuppkopplad belastningsutjämnare (klassisk) i PowerShell

[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad belastningsutjämnare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Konfigurera en belastningsutjämnare med hjälp av PowerShell

Följ stegen nedan om du vill konfigurera en belastningsutjämnare med hjälp av PowerShell:

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.
2. När du har skapat en virtuell dator kan du använda PowerShell-cmdlets för att lägga till en belastningsutjämnare till en virtuell dator i samma molntjänst.

I följande exempel lägger du till en belastningsutjämnare kallad ”webfarm” till molntjänsten ”mytestcloud” (eller myctestcloud.cloudapp.net) genom att lägga till slutpunkter för belastningsutjämnaren till de virtuella datorerna ”web1” och ”web2”. Belastningsutjämnaren tar emot nätverkstrafik på port 80 och belastningsutjämnar mellan de virtuella datorerna som definierats av den lokala slutpunkten (i det här fallet port 80) med hjälp av TCP.

### <a name="step-1"></a>Steg 1

Skapa en belastningsutjämnad slutpunkt för den första virtuella datorn, ”web1”

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Steg 2

Skapa en till slutpunkt för den andra virtuella datorn, ”web2”, och använd samma namn för belastningsutjämningsuppsättningen

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Ta bort en virtuell dator från en belastningsutjämnare

Du kan använda Remove-AzureEndpoint för att ta bort slutpunkten för en virtuell dator från belastningsutjämnaren

```powershell
    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också läsa mer om hur du [kommer igång med att skapa en intern belastningsutjämnare](load-balancer-get-started-ilb-classic-ps.md) och hur du konfigurerar typen av [distributionsläge](load-balancer-distribution-mode.md) för ett specifikt trafikbeteende i ett belastningsutjämnat nätverk.

Om ditt program kräver aktiva anslutningar för servrar bakom en belastningsutjämnare rekommenderar vi att du läser mer om [timeout-inställningar för inaktiv TCP för en belastningsutjämnare](load-balancer-tcp-idle-timeout.md). Den här artikeln innehåller information om beteendet vid inaktiva anslutningar när du använder Azure Load Balancer.



<!--HONumber=Nov16_HO2-->


