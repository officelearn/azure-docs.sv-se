---
title: Skala azure-skalningsuppsättningar för vertikalt skala
description: Så här skalar du en virtuell dator vertikalt som svar på övervakningsaviseringar med Azure Automation
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: manayar
ms.openlocfilehash: fa1dda2907e8400491c8d18897bb41fb9cff49fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76274439"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Vertikal automatisk skalning med skalningsuppsättningar för virtuella datorer

I den här artikeln beskrivs hur du lodrätt skalar Azure [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) med eller utan att ometablera. 

Vertikal skalning, även känd som *skala upp* och *skala ner*, innebär att öka eller minska virtuella datorstorlekar (VM) som svar på en arbetsbelastning. Jämför det här beteendet med [vågrät skalning](virtual-machine-scale-sets-autoscale-overview.md), även kallad *skala ut* och *skala i*, där antalet virtuella datorer ändras beroende på arbetsbelastningen.

Ometablering innebär att du tar bort en befintlig virtuell dator och ersätter den med en ny. När du ökar eller minskar storleken på virtuella datorer i en skaluppsättning för virtuella datorer vill du i vissa fall ändra storlek på befintliga virtuella datorer och behålla dina data, medan du i andra fall måste distribuera nya virtuella datorer av den nya storleken. Detta dokument täcker båda fallen.

Vertikal skalning kan vara användbart när:

* En tjänst som bygger på virtuella datorer är underutnyttjad (till exempel på helger). Om du minskar storleken på den virtuella datorn kan du minska månadskostnaderna.
* Öka vm-storleken för att hantera större efterfrågan utan att skapa ytterligare virtuella datorer.

Du kan ställa in lodrät skalning som ska utlösas baserat på måttbaserade aviseringar från din skalauppsättning för virtuella datorer. När aviseringen är aktiverad utlöses en webhook som utlöser en runbook som kan skala upp eller ned. Vertikal skalning kan konfigureras genom att följa dessa steg:

1. Skapa ett Azure Automation-konto med körningsfunktioner.
2. Importera Azure Automation Vertical Scale runbooks för virtuella datorskalauppsättningar till din prenumeration.
3. Lägg till en webhook i din runbook.
4. Lägg till en avisering i din skalauppsättning för virtuella datorer med hjälp av ett webhook-meddelande.

> [!NOTE]
> På grund av storleken på den första virtuella datorn kan de storlekar som den kan skalas till begränsas på grund av tillgängligheten för de andra storlekarna i den virtuella datorn distribueras i klustret. I de publicerade automatiseringskörningsböckerna som används i den här artikeln tar vi hand om det här fallet och skalar endast under de under virtuella paren. Detta innebär att en Standard_D1v2 virtuell dator inte plötsligt skalas upp till Standard_G5 eller skalas ned till Basic_A0. Det går inte heller att skapa begränsade storlekar för virtuella datorer som skalas upp/ned. Du kan välja att skala mellan följande färgpar:
> 
> | Skalningspar för VM-storlekar |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Skapa ett Azure Automation-konto med körningsfunktioner
Det första du behöver göra är att skapa ett Azure Automation-konto som är värd för runbooks som används för att skala den virtuella datorn skalningsuppsättning instanser. Nyligen [Azure Automation](https://azure.microsoft.com/services/automation/) introducerade "Kör som konto" funktionen som gör att ställa in Tjänsten Huvudansvarig för att automatiskt köra runbooks för en användares räkning. Mer information finns i:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera Azure Automation Vertical Scale-runbooks till din prenumeration

Runbooks som behövs för att lodrätt skala dina skaluppsättningar för virtuella datorer publiceras redan i Azure Automation Runbook Gallery. Så här importerar du dem till prenumerationen:

* [Runbook- och modulgallerier för Azure Automation](../automation/automation-runbook-gallery.md)

Välj alternativet Bläddra i galleri på Runbooks-menyn:

![Runbooks som ska importeras][runbooks]

De runbooks som måste importeras visas. Välj runbook baserat på om du vill ha vertikal skalning med eller utan att ometablera:

![Galleri för Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Lägga till en webhook i din runbook

När du har importerat runbooks, lägga till en webhook till runbook så att den kan utlösas av en avisering från en virtuell dator skala uppsättning. Information om hur du skapar en webhook för runbook beskrivs i den här artikeln:

* [Azure Automation-webbkrokar](../automation/automation-webhooks.md)

> [!NOTE]
> Se till att du kopierar webhook URI innan du stänger webhook dialogrutan som du behöver den här adressen i nästa avsnitt.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Lägga till en avisering i skalningsuppsättningen för den virtuella datorn

Nedan visas ett PowerShell-skript som visar hur du lägger till en avisering i en skalningsuppsättning för virtuella datorer. Se följande artikel för att få namnet på måttet att avfyra aviseringen på: [Azure Monitor automatisk skalning av vanliga mått](../azure-monitor/platform/autoscale-common-metrics.md).

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> Vi rekommenderar att du konfigurerar ett rimligt tidsfönster för aviseringen för att undvika att utlösa vertikal skalning och eventuella tillhörande avbrott i tjänsten, alltför ofta. Tänk dig ett fönster på minst 20-30 minuter eller mer. Överväg vågrät skalning om du behöver undvika avbrott.
> 
> 

Mer information om hur du skapar aviseringar finns i följande artiklar:

* [Snabbstartsexempel för Azure Monitor PowerShell](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Snabbstartsexempel för Azure Monitor cross-platform CLI](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Sammanfattning

Den här artikeln visade enkla exempel på vertikal skalning. Med dessa byggstenar - Automation-konto, runbooks, webhooks, varningar - kan du ansluta ett rikt utbud av händelser med en anpassad uppsättning åtgärder.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
