---
title: "Lodrätt skala skalningsuppsättningar i virtuella Azure-datorn | Microsoft Docs"
description: "Hur man lodrätt skala en virtuell dator för att övervaka aviseringar med Azure Automation"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: madhana
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: guybo
ms.openlocfilehash: 9159a5a9041864fe06785829121233379c46bb03
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Lodrät Autoskala med skaluppsättningar för den virtuella datorn
Den här artikeln beskriver hur man lodrätt skala Azure [Skalningsuppsättningar i virtuella](https://azure.microsoft.com/services/virtual-machine-scale-sets/) med eller utan reprovisioning. Lodrät skalning för virtuella datorer som inte är i skalningsuppsättningar, finns i [lodrätt skala virtuella Azure-datorn med Azure Automation](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Lodrät skalning, även kallat *skala upp* och *skala*, betyder att öka eller minska storlekar för virtuella datorer (VM) som svar på en arbetsbelastning. Jämför detta med [teckenbredden](virtual-machine-scale-sets-autoscale-overview.md), vilket även kallas *skala ut* och *skala i*, där antalet VMs ändras beroende på arbetsbelastningen.

Reprovisioning innebär att ta bort en befintlig virtuell dator och ersätta den med en ny. När du ökar eller minskar storleken på virtuella datorer i en Skaluppsättning i vissa fall vill du ändra storlek på befintliga virtuella datorer och behålla dina data, medan i andra fall måste du distribuera nya virtuella datorer i den nya storleken. Det här dokumentet beskriver båda fallen.

Lodrät skalning kan vara användbart när:

* En tjänst som bygger på virtuella datorer är outnyttjad (till exempel på helger). Minska VM-storlek kan minska kostnaderna för varje månad.
* Öka storleken på virtuella datorn ska kunna hantera större begäran utan att skapa ytterligare virtuella datorer.

Du kan ställa in lodräta skalning ska utlösta baserat på mått baserade aviseringar från din Skaluppsättning. När aviseringen aktiveras utlöses en webhook som utlösare för en runbook som kan skalas nivå ange uppåt eller nedåt. Lodrät skalning kan konfigureras genom att följa dessa steg:

1. Skapa ett Azure Automation-konto med Kör som-kapacitet.
2. Importera Lodrät skala för Azure Automation-runbooks för Skalningsuppsättningar i din prenumeration.
3. Lägg till en webhook i din runbook.
4. Lägga till en avisering till din VM Scale Set med hjälp av ett webhook-meddelande.

> [!NOTE]
> Lodrät autoskalning kan bara ske inom vissa intervall för VM-storlekar. Jämför specifikationerna för varje storlek innan du beslutar att skala från varandra (högre nummer inte alltid anger större VM-storlek). Du kan välja att skala mellan de följande paren storlekar:
> 
> | VM-storlekar skalning par |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Skapa ett Azure Automation-konto med Kör som-kapacitet
Det första du behöver göra är att skapa ett Azure Automation-konto som ska vara värd för runbooks som används för att anpassa VM Scale Set-instanser. Nyligen [Azure Automation](https://azure.microsoft.com/services/automation/) introducerade funktionen ”Kör som-konto” som gör att du kan konfigurera tjänstens huvudnamn för att automatiskt köra runbooks på en användares vägnar mycket enkelt. Mer information finns i artikeln nedan:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera Lodrät skala för Azure Automation-runbooks till din prenumeration
Runbooks som behövs för att skala din Skalningsuppsättningar lodrätt har redan publicerats i Azure Automation Runbook-galleriet. Om du vill importera följer dem till din prenumeration du stegen i den här artikeln:

* [Azure Automation Runbook- och stänga](../automation/automation-runbook-gallery.md)

Välj alternativet Bläddra galleriet Runbooks-menyn:

![Runbooks som ska importeras][runbooks]

Runbooks som ska importeras visas. Välj runbook baserat på om du vill lodräta skalning med eller utan reprovisioning:

![Runbooks-galleriet][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Lägga till en webhook i din runbook
När du har importerat förse runbooks måste du en webhook runbook så att den kan aktiveras av en avisering från en Skaluppsättning. Information om hur du skapar en webhook för din Runbook beskrivs i den här artikeln:

* [Azure Automation-webhooks](../automation/automation-webhooks.md)

> [!NOTE]
> Kontrollera att du kopierar webhook URI innan du stänger dialogrutan webhook som du behöver det i nästa avsnitt.
> 
> 

## <a name="add-an-alert-to-your-vm-scale-set"></a>Lägga till en avisering till din Skaluppsättning
Nedan finns ett PowerShell-skript som visar hur du lägger till en avisering till en Skaluppsättning. Finns i följande artikel för att hämta namnet på måttet ska utlösa aviseringen på: [Azure-Monitor autoskalning vanliga mått](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
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
> Det rekommenderas att konfigurera en rimlig tidsfönster för aviseringen för att undvika utlösa lodräta skalning och alla associerade avbrott, för ofta. Överväg ett fönster med minst 20 – 30 minuter eller mer. Överväg att vågräta skalning om du vill undvika avbrott.
> 
> 

Mer information om hur du skapar aviseringar finns i följande artiklar:

* [Azure-Monitor PowerShell Snabbstart-exempel](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Azure CLI för övervakaren plattformsoberoende Snabbstart-exempel](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Sammanfattning
Den här artikeln visade enkla lodräta skalning exempel. Du kan ansluta många olika händelser med en anpassad uppsättning åtgärder med byggblocken - Automation-konto, runbooks, webhooks, aviseringar.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
