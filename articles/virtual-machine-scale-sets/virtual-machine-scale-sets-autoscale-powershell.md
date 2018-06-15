---
title: Autoskala virtuella skalningsuppsättningarna med Azure PowerShell | Microsoft Docs
description: Hur du skapar automatiska regler för den virtuella datorn anger med Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8928e56f353858234db314714d411a9c2990eb4e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201474"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Skala automatiskt en virtuell dator-skala med Azure PowerShell
När du skapar en skaluppsättning för definiera antalet VM-instanser som du vill köra. När din begäran för program ändras, kan du automatiskt öka eller minska antalet VM-instanser. Möjligheten att Autoskala kan du Håll dig uppdaterad med kundernas behov eller svara på ändringar i programmet prestanda under hela livscykeln för din app.

Den här artikeln visar hur du skapar automatiska regler med Azure PowerShell som övervaka prestanda för VM-instanser i en skaluppsättning. Reglerna Autoskala öka eller minska antalet VM-instanser som svar på dessa prestandamått. Du kan också utföra dessa steg med den [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md) eller i den [Azure-portalen](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Förutsättningar
Du behöver en befintlig virtuell dator för att skapa regler för automatiska skaluppsättning. Du kan skapa en skala som anges med den [Azure-portalen](virtual-machine-scale-sets-create-portal.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md), eller [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md).

Att göra det enklare att skapa automatiska regler, definiera vissa variabler för din skaluppsättning. I följande exempel definieras variabler för skaluppsättningen namngivna *myScaleSet* i resursgrupp med namnet *myResourceGroup* och i den *östra USA* region. Din prenumeration hämta ID med [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Om du har flera prenumerationer som är kopplade till ditt konto, returneras endast det första abonnemanget. Justera namn och prenumerations-ID på följande sätt:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Skapa en regel för att automatiskt skala ut
Om ditt program begäran ökar, ange ökar belastningen på VM-instanser i nivå. Om den här ökade belastningen är konsekvent, i stället för bara en kort begäran, kan du konfigurera automatiska regler för att öka antalet VM-instanser i skaluppsättning. När dessa VM-instanser som skapas och dina program distribueras börjar skaluppsättning distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, t.ex CPU eller disk, hur länge belastningen program måste uppfylla ett visst tröskelvärde och ange hur många VM-instanser som ska läggas till skalan.

Nu ska vi skapa en regel med [ny AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) som ökar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen är större än 70% under en period på 5 minuter. När regeln utlöser ökar antalet VM-instanser med 20%. I skalningsuppsättningar med ett litet antal VM-instanser som du kan lämna `-ScaleActionScaleType` och endast ange `-ScaleActionValue` att öka genom *1* eller *2* instanser. VM-instanser kan vara mer lämpliga i skalningsuppsättningar med ett stort antal VM-instanser, en ökning av 10% eller 20%.

Följande parametrar används för den här regeln:

| Parameter               | Förklaring                                                                                                         | Värde          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Prestanda-mått för att övervaka och tillämpa skala ange åtgärder på.                                                   | Processorprocentandel |
| *-TimeGrain*            | Hur ofta mätvärdena som har samlats in för analys.                                                                   | 1 minut       |
| *-MetricStatistic*      | Definierar hur mätvärdena som samlats in ska aggregeras för analys.                                                | Medel        |
| *-TimeWindow*           | Tidsperiod som övervakas innan värdena mått och tröskelvärdet jämförs.                                   | 10 minuter      |
| *-Operatorn*             | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än   |
| *-Tröskelvärde*            | Det värde som regeln Autoskala kan utlösa en åtgärd.                                                      | 70%            |
| *-ScaleActionDirection* | Anger om skaluppsättning bör skala upp eller ned när regeln gäller.                                             | Höj       |
| *– ScaleActionScaleType* | Anger att antalet VM-instanser som ska ändras som ett procenttal.                                 | Procentuell förändring |
| *-ScaleActionValue*     | Procentandelen av VM-instanser som ska ändras när regeln utlöser.                                            | 20             |
| *-ScaleActionCooldown*  | Hur lång tid ska gå innan regeln tillämpas igen så att automatiska åtgärder har tid att börja gälla. | 5 minuter      |

I följande exempel skapas ett objekt med namnet *myRuleScaleOut* som innehåller den här skalan regel. Den *- MetricResourceId* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppens namn och skala namn:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Skapa en regel för att skala automatiskt i
På en kväll eller helger minska program-begäran. Om det här minskar belastningen är konsekvent under en viss tidsperiod, kan du konfigurera automatiska regler för att minska antalet VM-instanser i skaluppsättning. Den här åtgärden för skala minskar kostnaden för att köra skaluppsättningen eftersom du bara köra antalet instanser som krävs för att uppfylla den aktuella begäran.

Skapa en regel med [ny AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) som minskar antalet VM-instanser i en skala som anges när Genomsnittlig CPU-belastningen sedan sjunker under 30% över 10-minutersperiod. När regeln utlöser minskade antalet VM-instanser med 20%. I följande exempel skapas ett objekt med namnet *myRuleScaleDown* som innehåller den här skalan regel. Den *- MetricResourceId* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppens namn och skala namn:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definiera en Autoskala-profil
Om du vill associera Autoskala-regler med en skala, kan du skapa en profil. Autoskala profil definierar standard, lägsta och högsta mängd kapacitet och associerar Autoskala-regler. Skapa en profil för Autoskala med [ny AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). I följande exempel anger standardinställningen och minimum kapacitet *2* VM-instanser och högst *10*. Skala ut och kopplas sedan skalan i regler som har skapats i föregående steg:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Tillämpa automatiska regler för en skaluppsättning
Det sista steget är att använda Autoskala profil till din skaluppsättning. Nivå kan sedan automatiskt skala in eller ut baserat på begäran för program. Använda Autoskala profil med [Lägg till AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) på följande sätt:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Övervaka antalet instanser i en skaluppsättning
Visa en lista över instanser för att se antalet och status för VM-instanser i nivå med [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). Statusen anger om den Virtuella datorinstansen etablering som skaluppsättningen automatiskt skalas ut eller är avetablering som skalan skalas automatiskt i. I följande exempel visar VM instansens status för skaluppsättningen namngivna *myScaleSet* i resursgrupp med namnet *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Autoskala enligt ett schema
I föregående exempel skalas automatiskt en skala ställa in eller ut med grundläggande värden, till exempel CPU-användning. Du kan också skapa automatiska regler baserat på scheman. Reglerna schemabaserade kan du skala automatiskt antalet VM-instanser i en förväntad ökning i programmet begäran, till exempel core arbetstid, och sedan automatiskt skala antalet instanser samtidigt som du planerar mindre begäran som det är helgen.

Använd Azure-portalen för att skapa automatiska regler baserat på ett mått för schemat i stället för värden. Schema-baserade regler kan för närvarande inte skapas med Azure PowerShell.


## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder automatiska regler för att skala horisontellt och öka eller minska den *nummer* i VM-instanser i nivå. Du kan även skala lodrätt för att öka eller minska den Virtuella datorinstansen *storlek*. Mer information finns i [lodräta Autoskala med Virtual Machine-skalningsuppsättningar](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Information om hur du hanterar dina VM-instanser finns [hantera virtuella skalningsuppsättningarna med Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Information om hur du genererar aviseringar när din Autoskala regler utlösaren finns [använda automatiska åtgärder för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Du kan också [Använd granskningsloggarna för att skicka e-post och webhook aviseringar i Azure-Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
