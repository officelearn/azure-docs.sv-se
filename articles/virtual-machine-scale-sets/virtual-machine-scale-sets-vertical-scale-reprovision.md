---
title: Lodrätt skalning av Azure VM-skalningsuppsättningar | Microsoft Docs
description: Så här lodrätt skalning av en virtuell dator som svar på övervakning av aviseringar med Azure Automation
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: manayar
ms.openlocfilehash: c27d92a330d82cb8638a970602f2a8d0ce2e79c2
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579758"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Anger vertikal automatisk skalning med VM-skalningsuppsättning

Den här artikeln beskrivs hur du skalar Azure lodrätt [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/) med eller utan reprovisioning. Vertikal skalning av virtuella datorer som inte ingår i skalningsuppsättningar finns i [lodrätt skalning av Azure-dator med Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Vertikal skalning, även kallat *skala upp* och *Nedskalning*, innebär att öka eller minska storlekar för virtuella datorer (VM) som svar på en arbetsbelastning. Jämför det här beteendet med [horisontell skalning](virtual-machine-scale-sets-autoscale-overview.md), vilket även kallas *skala ut* och *skala i*, där antalet virtuella datorer ändras beroende på arbetsbelastningen.

Reprovisioning innebär att ta bort en befintlig virtuell dator och ersätta den med en ny. När du ökar eller minskar storleken på virtuella datorer i en virtuell datorskalning ange, i vissa fall som du vill ändra storlek på befintliga virtuella datorer och behålla dina data i andra fall du behöver distribuera nya virtuella datorer av den nya storleken. Det här dokumentet beskriver båda fallen.

Vertikal skalning kan vara användbart när:

* En tjänst som bygger på virtuella datorer är underutnyttjade (till exempel på helger). Minska virtuella datorstorlek kan minska månadskostnad.
* Öka storleken för att hantera större begäran utan att skapa ytterligare virtuella datorer.

Du kan ställa in vertikal skalning ska vara utlösta utifrån baserat måttaviseringar från virtual machine scale Sets. När aviseringen har aktiverats, utlöses en webhook som utlösare som en runbook som kan skala din skalningsuppsättning ange uppåt eller nedåt. Vertikal skalning kan konfigureras genom att följa dessa steg:

1. Skapa ett Azure Automation-konto med Kör som-funktioner.
2. Importera vertikal skalning i Azure Automation-runbooks för skalningsuppsättningar för virtuella datorer till din prenumeration.
3. Lägg till en webhook i din runbook.
4. Lägga till en avisering till din skalningsuppsättning med hjälp av ett webhook-meddelande.

> [!NOTE]
> På grund av storleken på den första virtuella datorn, storlekar kan den skalas till, kan begränsas på grund av den aktuella virtuella datorn har distribuerats i tillgängligheten för andra storlekar i klustret. I den publicerade automation-runbooks som används i den här artikeln vi tar hand om det här fallet och kan endast skalas inom den nedan par för VM-storlek. Det innebär att en virtuell dator för Standard_D1v2 inte plötsligt ska skalas upp till Standard_G5 eller skalas till Basic_A0. Också stöds begränsad VM storlekar skala upp och ned inte. Du kan välja att skala mellan de följande paren storlekar:
> 
> | VM-storlekar som skalning par |  |
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
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Skapa ett Azure Automation-konto med Kör som-funktioner
Det första du behöver göra är att skapa ett Azure Automation-konto som är värd för runbooks som används för att skala skalningsuppsättningsinstanserna för virtuell dator. Nyligen [Azure Automation](https://azure.microsoft.com/services/automation/) introducerade funktionen ”Kör som-konto” som gör att du kan konfigurera tjänstens huvudnamn för att automatiskt köra runbooks på en användares räkning. Mer information finns i:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera vertikal skalning i Azure Automation-runbooks till din prenumeration
Runbooks som behövs för att lodrätt skalning av VM-skalningsuppsättningar har redan publicerats i Azure Automation Runbook-galleriet. Om du vill importera följer dem till din prenumeration du stegen i den här artikeln:

* [Runbook- och modulgallerier för Azure Automation](../automation/automation-runbook-gallery.md)

Välj alternativet Sök i galleri Runbooks-menyn:

![Runbooks som ska importeras][runbooks]

De runbooks som ska importeras visas. Välj aktuell runbook baserat på om du vill lodrät skalning med eller utan reprovisioning:

![Runbookgalleri][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Lägg till en webhook i din runbook
När du har importerat runbooks, lägga till en webhook för runbooken så att den kan aktiveras via en avisering från en skalningsuppsättning för virtuell dator. För att skapa en webhook för din Runbook beskrivs i den här artikeln:

* [Azure Automation-webhookar](../automation/automation-webhooks.md)

> [!NOTE]
> Kontrollera att du kopierar webhooken URI: N innan du stänger dialogrutan webhook eftersom du behöver den här adressen i nästa avsnitt.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Lägga till en avisering till din skalningsuppsättning för virtuell dator
Nedan anges ett PowerShell-skript som visar hur du lägger till en avisering till en virtuell datorskalning. Se följande artikel för att hämta namnet på mått som ska utlösa aviseringen på: [Azure Monitor autoskalning vanliga mått](../azure-monitor/platform/autoscale-common-metrics.md).

```
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
> Vi rekommenderar att konfigurera en rimlig tidsperiod för aviseringen för att undvika utlösa vertikal skalning och associerade avbrott i tjänsten, för ofta. Överväg ett fönster med minst 20 – 30 minuter eller mer. Överväg att horisontell skalning om du vill undvika avbrott.
> 
> 

Mer information om hur du skapar aviseringar finns i följande artiklar:

* [Azure Monitor PowerShell quickstart-exempel](../azure-monitor/platform/powershell-quickstart-samples.md)
* [Azure Monitor plattformsoberoende CLI quickstart-exempel](../azure-monitor/platform/cli-samples.md)

## <a name="summary"></a>Sammanfattning
Den här artikeln visade enkla exempel för lodrät skalning. Med dessa byggstenar - Automation-konto, runbooks, webhooks, aviseringar – kan du ansluta en rad olika händelser med en anpassad uppsättning åtgärder.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
