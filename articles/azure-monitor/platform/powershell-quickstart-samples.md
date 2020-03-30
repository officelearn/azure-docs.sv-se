---
title: Snabbstartsexempel för Azure Monitor PowerShell
description: Använd PowerShell för att komma åt Azure Monitor-funktioner som automatisk skalning, aviseringar, webhooks och söka aktivitetsloggar.
ms.subservice: ''
ms.topic: conceptual
ms.date: 2/14/2018
ms.openlocfilehash: 9f039f71954998ef561d1efd1e559318740c86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274325"
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Snabbstartsexempel för Azure Monitor PowerShell
I den här artikeln visas exempel på PowerShell-kommandon som hjälper dig att komma åt Azure Monitor-funktioner.

> [!NOTE]
> Azure Monitor är det nya namnet på det som kallades "Azure Insights" fram till den 25 september 2016. Namnområdena och därmed följande kommandon innehåller dock fortfarande ordet "insikter".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurera PowerShell
Om du inte redan har gjort det konfigurerar du PowerShell så att det körs på datorn. Mer information finns i [Så här installerar och konfigurerar du PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exempel i den här artikeln
Exemplen i artikeln visar hur du kan använda Azure Monitor-cmdlets. Du kan också granska hela listan över Azure Monitor PowerShell-cmdlets på [Azure Monitor (Insights) Cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Logga in och använda prenumerationer
Logga först in på din Azure-prenumeration.

```powershell
Connect-AzAccount
```

Du ser en inloggningsskärm. När du loggar in visas ditt konto, TenantID och standardprenumerations-ID. Alla Azure-cmdlets fungerar i samband med din standardprenumeration. Om du vill visa listan över prenumerationer som du har åtkomst till använder du följande kommando:

```powershell
Get-AzSubscription
```

Om du vill se din arbetskontext (vilken prenumeration dina kommandon körs mot) använder du följande kommando:

```powershell
Get-AzContext
```
Om du vill ändra arbetskontexten till en annan prenumeration använder du följande kommando:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Hämta aktivitetslogg för en prenumeration
Använd [Cmdlet Get-AzLog.](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog)  Följande är några vanliga exempel. Aktivitetsloggen innehåller de senaste 90 dagarnas åtgärder. Om du använder datum före den här tiden visas ett felmeddelande.  

Se vad det aktuella datumet/tiden är för att kontrollera vilka tider som ska användas i kommandona nedan:
```powershell
Get-Date
```

Hämta loggposter från den här tiden/datumet för att presentera:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Hämta loggposter mellan ett tids-/datumintervall:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta loggposter från en viss resursgrupp:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta loggposter från en viss resursprovider mellan ett tids-/datumintervall:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta alla loggposter med en specifik uppringare:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Följande kommando hämtar de senaste 1000 händelserna från aktivitetsloggen:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog`många andra parametrar. Se `Get-AzLog` referensen för mer information.

> [!NOTE]
> `Get-AzLog`ger bara 15 dagars historia. Med parametern **-MaxRecords** kan du fråga de senaste N-händelserna, längre än 15 dagar. Om du vill komma åt händelser som är äldre än 15 dagar använder du REST API- eller SDK-exemplet (C#med hjälp av SDK). Om du inte inkluderar **StartTime**är standardvärdet **EndTime** minus en timme. Om du inte inkluderar **EndTime**är standardvärdet aktuell tid. Alla tider är i UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Hämta aviseringar historik
Om du vill visa alla aviseringshändelser kan du fråga Azure Resource Manager-loggarna med hjälp av följande exempel.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Om du vill visa historiken för `Get-AzAlertHistory` en viss aviseringsregel kan du använda cmdleten och skicka in resurs-ID:t för aviseringsregeln.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Cmdleten `Get-AzAlertHistory` stöder olika parametrar. Mer information finns i [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Hämta information om varningsregler
Alla följande kommandon fungerar på en resursgrupp med namnet "montest".

Visa alla egenskaper för varningsregeln:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Hämta alla aviseringar i en resursgrupp:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Hämta alla varningsregler som angetts för en målresurs. Till exempel alla varningsregler som anges på en virtuell dator.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule`stöder andra parametrar. Mer information finns i [Get-AlertRule.](https://msdn.microsoft.com/library/mt282459.aspx)

## <a name="create-metric-alerts"></a>Skapa måttaviseringar
Du kan `Add-AlertRule` använda cmdlet för att skapa, uppdatera eller inaktivera en varningsregel.

Du kan skapa e-post- `New-AzAlertRuleEmail` `New-AzAlertRuleWebhook`och webhook-egenskaper med respektive . I cmdlet för aviseringsregeln tilldelar du dessa egenskaper som åtgärder till egenskapen **Åtgärder** i varningsregeln.

I följande tabell beskrivs de parametrar och värden som används för att skapa en avisering med hjälp av ett mått.

| parameter | värde |
| --- | --- |
| Namn |simpletestdiskwrite |
| Plats för den här varningsregeln |USA, östra |
| ResourceGroup |montest (montest) |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig /subscription |
| MetricName för aviseringen som skapas |\PhysicalDisk(_Total)\Diskskrivningar/sek. Se `Get-MetricDefinitions` cmdlet om hur du hämtar de exakta måttnamnen |
| operator |GreaterThan |
| Tröskelvärde (antal/sek in för det här måttet) |1 |
| WindowSize (hh:mm:ss-format) |00:05:00 |
| aggregator (statistik över måttet, som använder genomsnittligt antal, i det här fallet) |Medel |
| anpassade e-postmeddelanden (strängmatris) |'foo@example.com','bar@example.com' |
| skicka e-post till ägare, bidragsgivare och läsare |-SendToServiceOwners |

Skapa en e-poståtgärd

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Skapa en Webhook-åtgärd

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Skapa aviseringsregeln för CPU%-måttet på en klassisk virtuell dator

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Hämta varningsregeln

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Cmdlet för lägg till avisering uppdaterar också regeln om det redan finns en varningsregel för de angivna egenskaperna. Om du vill inaktivera en varningsregel inkluderar du parametern **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Få en lista över tillgängliga mått för aviseringar
Du kan `Get-AzMetricDefinition` använda cmdlet för att visa listan över alla mått för en viss resurs.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

I följande exempel genereras en tabell med måttnamnet och enheten för den.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

En fullständig lista över `Get-AzMetricDefinition` tillgängliga alternativ finns på [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Skapa och hantera aktivitetsloggaviseringar
Du kan `Set-AzActivityLogAlert` använda cmdlet för att ställa in en aktivitetsloggavisering. En aktivitetsloggavisering kräver att du först definierar dina villkor som en ordlista med villkor och sedan skapar en avisering som använder dessa villkor.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

De ytterligare webhook-egenskaperna är valfria. Du kan få tillbaka innehållet i `Get-AzActivityLogAlert`en aktivitetsloggavisering med .

## <a name="create-and-manage-autoscale-settings"></a>Skapa och hantera inställningar för automatisk skalning
En resurs (en webbapp, vm, molntjänst eller skaluppsättning för virtuella datorer) kan bara ha en automatisk skalningsinställning konfigurerad för den.
Varje inställning för automatisk skalning kan dock ha flera profiler. Till exempel en för en prestandabaserad skalprofil och en andra för en schemabaserad profil. Varje profil kan ha flera regler konfigurerade på den. Mer information om Automatisk skalning finns i [Så här skalar du ett program automatiskt](../../cloud-services/cloud-services-how-to-scale-portal.md).

Här är stegen för att använda:

1. Skapa regler.
2. Skapa profiler som mappar de regler som du skapade tidigare i profilerna.
3. Valfritt: Skapa meddelanden för automatisk skalning genom att konfigurera webhook- och e-postegenskaper.
4. Skapa en automatisk skalningsinställning med ett namn på målresursen genom att mappa de profiler och meddelanden som du skapade i föregående steg.

Följande exempel visar hur du kan skapa en automatisk skalningsinställning för en skaladuppsättning för virtuella datorer för ett Windows-operativsystem baserat med hjälp av måttet för cpu-användning.

Skapa först en regel att skala ut, med en ökning av antalet instanser.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Skapa sedan en regel att skala in, med en minskning av antalet instanser.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Skapa sedan en profil för reglerna.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Skapa en webhook-egenskap.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Skapa meddelandeegenskapen för inställningen för automatisk skalning, inklusive e-post och webhook som du skapade tidigare.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Slutligen skapar du inställningen för automatisk skalning för att lägga till profilen som du skapade tidigare. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Mer information om hur du hanterar inställningar för automatisk skalning finns i [Hämta automatisk skalning](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historik för automatisk skalning
I följande exempel visas hur du kan visa de senaste händelser för automatisk skalning och avisering. Använd aktivitetsloggsökningen för att visa historiken för automatisk skalning.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Du kan `Get-AzAutoScaleHistory` använda cmdlet för att hämta automatisk skalningshistorik.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Mer information finns i [Hämta-Automatisk skalaHistoria](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Visa information för en automatisk skalningsinställning
Du kan `Get-Autoscalesetting` använda cmdlet för att hämta mer information om inställningen för automatisk skalning.

I följande exempel visas information om alla inställningar för automatisk skalning i resursgruppen "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

I följande exempel visas information om alla inställningar för automatisk skalning i resursgruppen "myrg1" och särskilt inställningen för automatisk skalning med namnet "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Ta bort en inställning för automatisk skalning
Du kan `Remove-Autoscalesetting` använda cmdlet för att ta bort en automatisk skalningsinställning.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Hantera loggprofiler för aktivitetslogg
Du kan skapa en *loggprofil* och exportera data från din aktivitetslogg till ett lagringskonto och du kan konfigurera datalagring för den. Du kan också strömma data till händelsehubben. Den här funktionen är för närvarande i förhandsversion och du kan bara skapa en loggprofil per prenumeration. Du kan använda följande cmdlets med din aktuella prenumeration för att skapa och hantera loggprofiler. Du kan också välja en viss prenumeration. Även om PowerShell är standard för den aktuella prenumerationen kan du alltid ändra den med `Set-AzContext`. Du kan konfigurera aktivitetsloggen för att dirigera data till alla lagringskonto eller Event Hub inom den prenumerationen. Data skrivs som blob-filer i JSON-format.

### <a name="get-a-log-profile"></a>Skaffa en loggprofil
Om du vill hämta dina `Get-AzLogProfile` befintliga loggprofiler använder du cmdleten.

### <a name="add-a-log-profile-without-data-retention"></a>Lägga till en loggprofil utan datakvarhållning
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Ta bort en loggprofil
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Lägga till en loggprofil med datakvarhållning
Du kan ange egenskapen **-RetentionInDays** med antalet dagar, som ett positivt heltal, där data behålls.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Lägga till loggprofil med kvarhållning och EventHub
Förutom att dirigera dina data till lagringskontot kan du även strömma dem till en eventhub. I den här förhandsversionen är konfigurationen för lagringskonto obligatorisk, men konfigurationen av eventhubben är valfri.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurera diagnostikloggar
Många Azure-tjänster tillhandahåller ytterligare loggar och telemetri som kan göra något av följande: 
 - konfigureras för att spara data i ditt Azure Storage-konto
 - skickas till eventhubbar
 - skickas till en Log Analytics-arbetsyta. 

Åtgärden kan bara utföras på resursnivå. Lagringskontot eller händelsehubben ska finnas i samma region som målresursen där diagnostikinställningen är konfigurerad.

### <a name="get-diagnostic-setting"></a>Hämta diagnostikinställning
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Inaktivera diagnostikinställning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktivera diagnostikinställning utan kvarhållning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktivera diagnostikinställning med kvarhållning

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställning med kvarhållning för en viss loggkategori

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställning för händelsehubbar

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Aktivera diagnostikinställning för Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observera att egenskapen WorkspaceId tar *arbetsytans resurs-ID.* Du kan hämta resurs-ID:t för din Log Analytics-arbetsyta med följande kommando:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Dessa kommandon kan kombineras för att skicka data till flera destinationer.

