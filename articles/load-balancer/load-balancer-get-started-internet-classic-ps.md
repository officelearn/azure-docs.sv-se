---
title: Skapa en internetuppkopplad belastningsutjämnare – klassiska Azure PowerShell
titlesuffix: Azure Load Balancer
description: Lär dig hur du skapar en Internetuppkopplad lastbalanserare i klassiskt läge med hjälp av PowerShell
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 54ef8782620a387d60454023d0e446279e467a99
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516955"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Komma igång med att skapa en Internetuppkopplad lastbalanserare (klassisk) i PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Innan du börjar arbeta med Azure-resurser, är det viktigt att förstå att Azure för närvarande har två distributionsmodeller: Azure Resource Manager och klassisk. Se till att du förstår [distributionsmodeller och verktyg](../azure-classic-rm.md) innan du börjar arbeta med Azure-resurser. Du kan granska dokumentationen för olika verktyg genom att klicka på flikarna överst i den här artikeln. Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också läsa artikeln om [hur du skapar en Internetuppkopplad lastbalanserare med hjälp av Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Konfigurera en lastbalanserare med hjälp av PowerShell

Genomför följande steg om du vill konfigurera en lastbalanserare med hjälp av PowerShell:

1. Om du aldrig använt Azure PowerShell tidigare, se [Installera och konfigurera Azure PowerShell](/powershell/azure/overview) och följ instruktionerna till slutet för att logga in på Azure och välja din prenumeration.
2. När du har skapat en virtuell dator kan du använda PowerShell-cmdlets för att lägga till en lastbalanserare till en virtuell dator i samma molntjänst.

I följande exempel lägger du till en lastbalanserare kallad ”webfarm” till molntjänsten ”mytestcloud” (eller myctestcloud.cloudapp.net) genom att lägga till slutpunkter för lastbalanseraren till de virtuella datorerna ”web1” och ”web2”. Lastbalanseraren tar emot nätverkstrafik på port 80 och lastbalanserar mellan de virtuella datorerna som definierats av den lokala slutpunkten (i det här fallet port 80) med hjälp av TCP.

### <a name="step-1"></a>Steg 1

Skapa en belastningsutjämnad slutpunkt för den första virtuella datorn, ”web1”

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Steg 2

Skapa en till slutpunkt för den andra virtuella datorn, ”web2”, och använd samma namn för lastbalanseringsuppsättningen

```powershell
Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Ta bort en virtuell dator från en lastbalanserare

Du kan använda Remove-AzureEndpoint för att ta bort slutpunkten för en virtuell dator från lastbalanseraren

```powershell
Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin | Update-AzureVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också läsa mer om hur du [kommer igång med att skapa en intern lastbalanserare](load-balancer-get-started-ilb-classic-ps.md) och hur du konfigurerar typen av [distributionsläge](load-balancer-distribution-mode.md) för ett specifikt trafikbeteende i ett lastbalanserat nätverk.

Om ditt program kräver aktiva anslutningar för servrar bakom en lastbalanserare rekommenderar vi att du läser mer om [timeout-inställningar för inaktiv TCP för en lastbalanserare](load-balancer-tcp-idle-timeout.md). Den här artikeln innehåller information om beteendet vid inaktiva anslutningar när du använder Azure Load Balancer.
