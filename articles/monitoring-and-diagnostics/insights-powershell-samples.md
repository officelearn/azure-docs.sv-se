---
title: "Azure PowerShell övervakaren Snabbstart-exempel. | Microsoft Docs"
description: "Använda PowerShell för att få åtkomst till Azure-Monitor-funktioner, till exempel Autoskala, aviseringar, webhooks och söka aktivitetsloggar."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/14/2018
ms.author: robb
ms.openlocfilehash: 3479b9c5bc1c8c77d2c6012b40dc9cd8f8e1708b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Azure-Monitor PowerShell Snabbstart-exempel
Den här artikeln innehåller exempel av PowerShell-kommandon som hjälper dig att komma åt Azure-Monitor funktioner.

> [!NOTE]
> Azure övervakaren är det nya namnet för vad anropades ”Azure Insights” förrän den 25 september 2016. Dock namnområden och därmed följande kommandon fortfarande innehåller den ordet ”insikter”.
> 
> 

## <a name="set-up-powershell"></a>Konfigurera PowerShell
Om du inte redan gjort ange PowerShell ska köras på datorn. Mer information finns i [installera och konfigurera PowerShell](/powershell/azure/overview).

## <a name="examples-in-this-article"></a>Exemplen i den här artikeln
Exemplen i artikeln visar hur du kan använda Azure-Monitor-cmdlets. Du kan också granska hela listan med Azure-Monitor PowerShell-cmdlets på [Azure-Monitor (insikter) Cmdlets](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx).

## <a name="sign-in-and-use-subscriptions"></a>Logga in och använda prenumerationer
Först logga in på Azure-prenumerationen.

```PowerShell
Login-AzureRmAccount
```

Du ser ett tecken på skärmen. När du loggar in ditt konto, TenantID, och standard prenumerations-ID visas. Alla Azure cmdlets fungerar i samband med standard-prenumeration. Om du vill visa listan över prenumerationer som du har åtkomst till, använder du följande kommando:

```PowerShell
Get-AzureRmSubscription
```

Om du vill ändra den fungerande kontexten till en annan prenumeration, använder du följande kommando:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log-for-a-subscription"></a>Hämta aktivitetsloggen för en prenumeration
Använd den `Get-AzureRmLog` cmdlet.  Här följer några vanliga exempel.

Hämta loggposter från denna tid/datum presentera:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Hämta loggposter ett värdeområde tid/datum:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta loggposter från en viss resursgrupp:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Hämta loggposter från en viss resurs-leverantör ett värdeområde tid/datum:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta alla loggposter med en specifik anropare:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

Följande kommando hämtar de senaste 1 000 händelserna från aktivitetsloggen:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` har stöd för många parametrar. Finns det `Get-AzureRmLog` referens för mer information.

> [!NOTE]
> `Get-AzureRmLog` endast ger 15 dagar tidigare. Med hjälp av den **- MaxEvents** parameter kan du fråga de sista N händelserna efter 15 dagar. För åtkomst till händelser som är äldre än 15 dagar, använda REST API eller SDK (C#-exempel med hjälp av SDK). Om du inte inkluderar **StartTime**, då är standardvärdet **EndTime** minus en timme. Om du inte inkluderar **EndTime**, och standardvärdet är aktuell tid. Det finns alltid i UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Hämta aviseringar historik
Om du vill visa alla aviseringar händelser, kan du fråga Azure Resource Manager-loggar med hjälp av följande exempel.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Om du vill visa historiken för en specifik avisering regel som du kan använda den `Get-AzureRmAlertHistory` cmdlet, skicka i resurs-ID för regeln.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

Den `Get-AzureRmAlertHistory` cmdlet har stöd för olika parametrar. Mer information finns i [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Hämta information om Varningsregler
Alla följande kommandon fungerar på en resursgrupp med namnet ”montest”.

Visa alla egenskaper för regeln:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Hämta alla aviseringar på en resursgrupp:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Hämta alla Varningsregler för en målresurs. Till exempel ange alla Varningsregler på en virtuell dator.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` har stöd för andra parametrar. Se [Get-AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) för mer information.

## <a name="create-metric-alerts"></a>Skapa mått aviseringar
Du kan använda den `Add-AlertRule` för att skapa, uppdatera eller inaktivera en aviseringsregel.

Du kan skapa e-post och webhook-egenskaper med `New-AzureRmAlertRuleEmail` och `New-AzureRmAlertRuleWebhook`respektive. Tilldela dessa egenskaper i varningsregeln-cmdlet som åtgärder för att den **åtgärder** -egenskapen för regeln.

I följande tabell beskrivs de parametrar och värden som används för att skapa en avisering med ett mått.

| Parameter | värde |
| --- | --- |
| Namn |simpletestdiskwrite |
| Platsen för den här varningsregeln |Östra USA |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| MetricName för aviseringen som har skapats |\PhysicalDisk (_Total) \Disk Diskskrivningar/sek. Finns det `Get-MetricDefinitions` cmdlet om hur du hämtar de exakta mått namn |
| Operatorn |GreaterThan |
| Tröskelvärde (antal per sekund i för det här måttet) |1 |
| Fönsterstorlek (format: mm: ss) |00:05:00 |
| Aggregator (statistik på måttet, som använder Genomsnittligt antal i det här fallet) |Medel |
| anpassad e-postmeddelanden (Strängmatrisen) |'foo@example.com','bar@example.com' |
| Skicka e-post till ägare, deltagare och läsare |-SendToServiceOwners |

Skapa en e-åtgärd

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Skapa en Webhook-åtgärd

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Skapa varningsregeln på processor % mått på en klassisk virtuell dator

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Hämta varningsregeln

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Lägg till avisering cmdlet uppdateras också regeln om det finns redan en regel för varning för de angivna egenskaperna. Om du vill inaktivera en aviseringsregel, inkluderar du parametern **- DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Hämta en lista över tillgängliga mått för aviseringar
Du kan använda den `Get-AzureRmMetricDefinition` för att visa en lista över alla mätvärden för en viss resurs.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

I följande exempel skapar en tabell med måttet namn och enhet för den.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

En fullständig lista över tillgängliga alternativ för `Get-AzureRmMetricDefinition` finns på [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-activity-log-alerts"></a>Skapa och hantera aktivitetsloggen aviseringar
Du kan använda den `Set-AzureRmActivityLogAlert` för att ange en aktivitetsloggen avisering. En avisering i aktivitetsloggen kräver att du först definiera dina villkor som en ordlista över villkor och sedan skapa en avisering som använder dessa villkor.

```PowerShell

$condition1 = New-AzureRmActivityLogAlertCondition -Field 'category' -Equals 'Administrative'
$condition2 = New-AzureRmActivityLogAlertCondition -Field 'operationName' -Equals 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzureRmActionGroup -ActionGroupId 'actiongr1' -WebhookProperties $dict
Set-AzureRmActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/' -Action $actionGrp1 -Condition $condition1, $condition2

```

Ytterligare webhook-egenskaper är valfria. Du kan få tillbaka innehållet i en aktivitet loggen avisering med `Get-AzureRmActivityLogAlert`.

## <a name="create-and-manage-autoscale-settings"></a>Skapa och hantera Autoskala inställningar
En resurs (en webbapp, VM, molntjänst eller Skaluppsättning för virtuell dator) kan ha endast en autoskalningsinställning som konfigurerats för den.
Varje autoskalningsinställning kan dock ha flera profiler. Till exempel en för en prestandabaserad skala profil och en andra princip för en schemabaserade profil. Varje profil kan ha flera regler som konfigurerats på den. Läs mer om Autoskala [hur Autoskala ett program](../cloud-services/cloud-services-how-to-scale-portal.md).

Här följer stegen för att använda:

1. Skapa regler.
2. Skapa eller profilerna mappa de regler som du skapade tidigare till profilerna.
3. Valfritt: Skapa meddelanden om autoskalning genom att konfigurera egenskaper för webhook och e-post.
4. Skapa en autoskalningsinställning med ett namn på målresursen genom att mappa profiler och meddelanden som du skapade i föregående steg.

I följande exempel visas hur du kan skapa en autoskalningsinställning för en Virtual Machine Scale Set för ett Windows-operativsystem baserade genom att använda mått för CPU-användning.

Först skapa en regel för att skala ut, med en instans antal ökning.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Sedan skapa en regel för att skala, med en instans antal minska.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Skapa sedan en profil för reglerna.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Skapa en webhook-egenskap.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Skapa meddelande-egenskapen för autoskalningsinställning, inklusive e-post och webhooken som du skapade tidigare.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Skapa slutligen autoskalningsinställningen som du lägger till den profil som du skapade tidigare. 

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Mer information om hur du hanterar Autoskala inställningar finns [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Autoskala historik
I följande exempel visas hur du kan visa senaste Autoskala och avisering händelser. Använd aktiviteten loggen sökning om du vill visa Autoskala historiken.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Du kan använda den `Get-AzureRmAutoScaleHistory` för att hämta Autoskala historik.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Mer information finns i [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Visa information om en autoskalningsinställning
Du kan använda den `Get-Autoscalesetting` för att hämta mer information om autoskalningsinställningen.

I följande exempel visas information om alla Autoskala inställningar i resursen grupp 'myrg1'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

I följande exempel visas information om alla Autoskala inställningar i resursen grupp 'myrg1' och specifikt autoskalningsinställningen med namnet 'MyScaleVMSSSetting'.

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Ta bort en autoskalningsinställning
Du kan använda den `Remove-Autoscalesetting` för att ta bort en autoskalningsinställning.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Hantera loggen profiler för aktivitetsloggen
Du kan skapa en *logga profil* och exportera data från din aktivitetsloggen till ett lagringskonto och du kan konfigurera datalagring för den. Du kan också kan du också strömma data till din Event Hub. Den här funktionen är för närvarande under förhandsgranskning och du kan bara skapa en logg profil per prenumeration. Du kan använda följande cmdlets med din aktuella prenumeration för att skapa och hantera profiler för loggen. Du kan också välja en viss prenumeration. Även om PowerShell som standard den aktuella prenumerationen, du kan ändra den med hjälp av `Set-AzureRmContext`. Du kan konfigurera aktivitetsloggen att vidarebefordra data till storage-konto eller Händelsehubb i den prenumerationen. Data skrivs som blob-filer i JSON-format.

### <a name="get-a-log-profile"></a>Hämta en logg-profil
För att hämta din befintliga log-profiler använder det `Get-AzureRmLogProfile` cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Lägg till en logg profil utan datalagring
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Ta bort en logg-profil
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Lägg till en logg-profil med datalagring
Du kan ange den **- RetentionInDays** egenskap med antalet dagar som ett positivt heltal, där data bevaras.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Lägg till loggen profil med kvarhållning och EventHub
Förutom routning dina data till storage-konto strömma du den till en Händelsehubb. I den här förhandsversionen kontokonfigurationen lagring är obligatorisk men Event Hub-konfigurationen är valfria.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurera diagnostik-loggar
Många Azure-tjänster ger ytterligare loggar och telemetri som kan utföra en eller flera av följande: 
 - konfigureras för att spara data i Azure Storage-konto
 - skickas till Händelsehubbar
 - skickas till en OMS logganalys-arbetsytan. 

Åtgärden kan bara utföras på en resurs-nivå. Storage-konto eller händelse hubben ska finnas i samma region som målresursen där inställningen diagnostik konfigureras.

### <a name="get-diagnostic-setting"></a>Hämta diagnostikinställningen
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Inaktivera diagnostikinställningen

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Aktivera diagnostikinställningen utan kvarhållning

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Aktivera diagnostikinställningen med kvarhållning

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställningen med kvarhållning för en viss loggning kategori

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Aktivera diagnostikinställningen för Händelsehubbar

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Aktivera diagnostikinställningen för logganalys (OMS)

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Observera att egenskapen WorkspaceId tar den *resurs-ID* i arbetsytan. Du kan hämta resurs-ID för logganalys-arbetsytan med följande kommando:

```PowerShell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId

```

Dessa kommandon kan kombineras för att skicka data till flera mål.
