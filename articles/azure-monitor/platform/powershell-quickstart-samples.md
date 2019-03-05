---
title: Snabbstartsexempel för Azure Monitor PowerShell
description: Använda PowerShell för att få åtkomst till Azure Monitor-funktioner som automatisk skalning, aviseringar, webhooks och söka aktivitetsloggar.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/14/2018
ms.author: robb
ms.subservice: ''
ms.openlocfilehash: ae06fae8aa7706428a71b8069eff58ba8bf6abb1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307521"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Snabbstartsexempel för Azure Monitor PowerShell
Den här artikeln visar exempel på PowerShell-kommandon för att få åtkomst till Azure Monitor-funktioner.

> [!NOTE]
> Azure Monitor är det nya namnet för vad anropades ”Azure Insights” tills den 25 september 2016. Dock innehålla namnområden och därmed följande kommandon fortfarande ordet ”information”.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurera PowerShell
Om du inte redan gjort konfigurera PowerShell att köra på datorn. Mer information finns i [installera och konfigurera PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplen i den här artikeln
Exemplen i den här artikeln visar hur du kan använda Azure Monitor-cmdletar. Du kan också granska hela listan med Azure Monitor PowerShell-cmdlets på [Azure Monitor (insikter) Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.insights).

## <a name="sign-in-and-use-subscriptions"></a>Logga in och använda prenumerationer
Logga först in på Azure-prenumerationen.

```PowerShell
Connect-AzAccount
```

Du ser ett tecken på skärmen. När du loggar in ditt konto, TenantID, och standard prenumerations-ID visas. Alla Azure-cmdlets fungerar i kontexten för din Standardprenumeration. Om du vill visa en lista över prenumerationer som du har åtkomst till, använder du följande kommando:

```PowerShell
Get-AzSubscription
```

Om du vill ändra den fungerande kontexten till en annan prenumeration, använder du följande kommando:

```PowerShell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Hämta aktivitetsloggen för en prenumeration
Använd den `Get-AzLog` cmdlet.  Här följer några vanliga exempel.

Hämta poster från den här tid/datum att presentera:

```PowerShell
Get-AzLog -StartTime 2016-03-01T10:30
```

Hämta loggposter mellan flera tid/datum:

```PowerShell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta poster från en specifik resursgrupp:

```PowerShell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta poster från en viss resursprovider mellan flera tid/datum:

```PowerShell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta alla poster med en specifik anropare:

```PowerShell
Get-AzLog -Caller 'myname@company.com'
```

Följande kommando hämtar de senaste 1 000 händelserna från aktivitetsloggen:

```PowerShell
Get-AzLog -MaxEvents 1000
```

`Get-AzLog` har stöd för många parametrar. Se den `Get-AzLog` ha mer information.

> [!NOTE]
> `Get-AzLog` endast ger 15 dagar tidigare. Med hjälp av den **- MaxEvents** parametern kan du fråga efter de senaste N-händelserna efter 15 dagar. Använda REST API eller SDK (C#-exempel med hjälp av SDK) till åtkomsthändelser som är äldre än 15 dagar. Om du inte använder **StartTime**, och standardvärdet är **EndTime** minus en timme. Om du inte använder **EndTime**, och standardvärdet är aktuell tid. Hela tiden är i UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Hämta aviseringar historik
Om du vill visa alla händelser, kan du fråga Azure Resource Manager-loggar med hjälp av följande exempel.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Om du vill visa historik för en specifik aviseringsregel, du kan använda den `Get-AzAlertHistory` cmdlet, skicka i resurs-ID för regeln.

```PowerShell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Den `Get-AzAlertHistory` cmdlet har stöd för olika parametrar. Mer information finns i [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Hämta information om aviseringsregler
Alla följande kommandon fungerar på en resursgrupp med namnet ”montest”.

Visa alla egenskaper för regeln:

```PowerShell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Hämta alla aviseringar på en resursgrupp:

```PowerShell
Get-AzAlertRule -ResourceGroup montest
```

Hämta alla Varningsregler för en målresurs. Till exempel ange alla Varningsregler på en virtuell dator.

```PowerShell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` har stöd för andra parametrar. Se [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) för mer information.

## <a name="create-metric-alerts"></a>Skapa måttaviseringar
Du kan använda den `Add-AlertRule` cmdlet för att skapa, uppdatera eller inaktivera en aviseringsregel.

Du kan skapa e-post och webhook-egenskaper med hjälp av `New-AzAlertRuleEmail` och `New-AzAlertRuleWebhook`respektive. I cmdleten varningsregel tilldela dessa egenskaper som åtgärder för att den **åtgärder** egenskapen för regeln.

I följande tabell beskrivs de parametrar och värden som används för att skapa en avisering med mått.

| Parameter | värde |
| --- | --- |
| Namn |simpletestdiskwrite |
| Platsen för den här aviseringsregeln |Östra USA |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName om aviseringen som har skapats |\PhysicalDisk (_Total) \Disk Diskskrivningar/sek. Se den `Get-MetricDefinitions` cmdlet om hur du hämtar exakt tjänstmåttets namn |
| Operator |GreaterThan |
| Tröskelvärdet (antal per sekund i för det här måttet) |1 |
| Fönsterstorlek (: mm: ss-format) |00:05:00 |
| Aggregator (statistik för mått, som använder Genomsnittligt antal i det här fallet) |Medel |
| anpassade e-postmeddelanden (Strängmatrisen) |'foo@example.com','bar@example.com' |
| Skicka e-post till ägare, deltagare och läsare |-SendToServiceOwners |

Skapa en e poståtgärd

```PowerShell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Skapa en Webhook-åtgärd

```PowerShell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Skapa varningsregeln på CPU % mått på en klassisk virtuell dator

```PowerShell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Hämta varningsregeln

```PowerShell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Lägg till avisering cmdleten uppdaterar även regeln om det finns redan en varningsregel för de angivna egenskaperna. Om du vill inaktivera en aviseringsregel, inkluderar du parametern **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Hämta en lista över tillgängliga mått för aviseringar
Du kan använda den `Get-AzMetricDefinition` cmdlet för att visa en lista över alla mått för en specifik resurs.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id>
```

I följande exempel genererar en tabell med mått namn och en enhet för den.

```PowerShell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

En fullständig lista över tillgängliga alternativ för `Get-AzMetricDefinition` finns på [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Skapa och hantera aviseringar för aktivitetsloggen
Du kan använda den `Set-AzActivityLogAlert` cmdlet för att ange en aktivitetsloggavisering. En aktivitetsloggavisering kräver att du först definiera dina villkor som en ordlista med villkor och sedan skapa en avisering som använder dessa villkor.

```PowerShell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Ytterligare webhook-egenskaperna är valfria. Du kan gå tillbaka innehållet i en aktivitet Log avisering med `Get-AzActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Skapa och hantera inställningarna för automatisk skalning
En resurs (en webbapp, virtuell dator, molntjänst eller Virtual Machine Scale Sets) kan ha endast en autoskalningsinställning konfigurerat för den.
Varje inställning för automatisk skalning kan dock ha flera profiler. Till exempel en för en profil för prestandabaserad skala och en andra profil för en schemabaserad profil. Varje profil kan ha flera regler som konfigurerats på den. Läs mer om automatisk skalning, [hur att automatiskt skala ett program](../../cloud-services/cloud-services-how-to-scale-portal.md).

Här följer stegen för att använda:

1. Skapa regler.
2. Skapa profil(er) mappa de regler som du skapade tidigare till profilerna.
3. Valfritt: Skapa aviseringar för automatisk skalning genom att konfigurera egenskaper för webhook och e-post.
4. Skapa en autoskalningsinställning med ett namn på målresursen genom att mappa de profiler och meddelanden som du skapade i föregående steg.

I följande exempel visas hur du kan skapa en autoskalningsinställning för en Virtual Machine Scale Sets för ett Windows-operativsystem baserade med hjälp av mått för CPU-användning.

Börja med att skapa en regel för att skala ut, med en instans antal ökning.

```PowerShell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Därefter skapa en regel för att skala, med en instans antal minskningen.

```PowerShell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Skapa sedan en profil för reglerna.

```PowerShell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Skapa en webhook-egenskap.

```PowerShell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Skapa meddelande-egenskapen för autoskalningsinställningen, inklusive e-post och webhooken som du skapade tidigare.

```PowerShell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Skapa slutligen autoskalningsinställningen som du lägger till den profil som du skapade tidigare. 

```PowerShell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Läs mer om hur du hanterar inställningarna för automatisk skalning, [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historik för automatisk skalning
I följande exempel visas hur du kan visa de senaste automatisk skalning och händelser. Använda loggsökning aktivitet för att visa historik för automatisk skalning.

```PowerShell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Du kan använda den `Get-AzAutoScaleHistory` cmdlet för att hämta historiken för automatisk skalning.

```PowerShell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Mer information finns i [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Visa information om en autoskalningsinställning
Du kan använda den `Get-Autoscalesetting` cmdlet för att hämta mer information om autoskalningsinställningen.

I följande exempel visas information om alla inställningar för automatisk skalning i resource group ”myrg1”.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

I följande exempel visas information om alla inställningar för automatisk skalning i resource group ”myrg1” och specifikt autoskalningsinställning med namnet ”MyScaleVMSSSetting”.

```PowerShell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Ta bort en autoskalningsinställning
Du kan använda den `Remove-Autoscalesetting` cmdlet för att ta bort en autoskalningsinställning.

```PowerShell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Hantera loggprofiler för aktivitetslogg
Du kan skapa en *loggprofilen* och exportera data från din aktivitetslogg till ett lagringskonto och du kan konfigurera kvarhållning av data för den. Alternativt kan du också strömma data till din Event Hub. Den här funktionen är för närvarande preliminärt och du kan bara skapa en loggprofil per prenumeration. Du kan använda följande cmdletar med din aktuella prenumeration för att skapa och hantera loggprofiler. Du kan också välja en viss prenumeration. Även om PowerShell som standard den aktuella prenumerationen, du kan alltid ändra den med hjälp av `Set-AzContext`. Du kan konfigurera aktivitetsloggen att vidarebefordra data till alla lagringskonto eller Event Hub inom den prenumerationen. Data skrivs som blobfiler i JSON-format.

### <a name="get-a-log-profile"></a>Hämta en loggprofil
Om du vill hämta din befintliga loggprofiler, använda den `Get-AzLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Lägg till en loggprofil utan datakvarhållning
```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Ta bort en loggprofil
```PowerShell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Lägg till en loggprofil med en datakvarhållning
Du kan ange den **- RetentionInDays** egenskap med antalet dagar som ett positivt heltal, där data bevaras.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Lägg till loggprofil med kvarhållning och EventHub
Förutom routning dina data till storage-konto kan du också strömma det till en Händelsehubb. I den här förhandsversionen konfigurationen av storage-kontot är obligatorisk men Event Hub-konfigurationen är valfritt.

```PowerShell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurera diagnostikloggar
Många Azure-tjänster tillhandahåller ytterligare loggar och telemetri som kan utföra en eller flera av följande: 
 - konfigureras för att spara data i ditt Azure Storage-konto
 - skickas till Event Hubs
 - skickas till Log Analytics-arbetsytan. 

Åtgärden kan bara utföras på en resursnivå. Storage-konto eller event hub ska finnas i samma region som målresursen där diagnostik inställningen konfigureras.

### <a name="get-diagnostic-setting"></a>Hämta diagnostikinställning
```PowerShell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Inaktivera diagnostikinställning

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktivera diagnostikinställningar utan kvarhållning

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktivera diagnostikinställningar med kvarhållning

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställningar med kvarhållning för en viss loggning-kategori

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställningar för Event Hubs

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Aktivera diagnostikinställningar för Log Analytics

```PowerShell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observera att egenskapen WorkspaceId tar den *resurs-ID* i arbetsytan. Du kan hämta resurs-ID för Log Analytics-arbetsytan med följande kommando:

```PowerShell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Dessa kommandon kan kombineras för att skicka data till flera destinationer.

