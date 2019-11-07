---
title: Automatisera Azure Application insikter med PowerShell | Microsoft Docs
description: Automatisera att skapa och hantera resurser, aviseringar och tillgänglighets test i PowerShell med hjälp av en Azure Resource Manager mall.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 5ae043c356559b2e675f05af3eb7eb61973eb170
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621938"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Hantera Application Insights-resurser med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln visar hur du automatiserar skapandet och uppdateringen av [Application Insights](../../azure-monitor/app/app-insights-overview.md) resurser automatiskt med hjälp av Azure Resource Management. Du kan till exempel göra detta som en del av en build-process. Tillsammans med den grundläggande Application Insights-resursen kan du skapa [tillgänglighets webbtester](../../azure-monitor/app/monitor-web-app-availability.md), konfigurera [aviseringar](../../azure-monitor/app/alerts.md), ange [pris schema](pricing.md)och skapa andra Azure-resurser.

Nyckeln för att skapa dessa resurser är JSON-mallar för [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). Den grundläggande proceduren är: Ladda ned JSON-definitionerna för befintliga resurser. Parameterisera vissa värden, t. ex. namn; och kör sedan mallen när du vill skapa en ny resurs. Du kan paketera flera resurser för att skapa dem i en enda Go-till exempel en app monitor med tillgänglighets test, aviseringar och lagring för kontinuerlig export. Det finns vissa nyanser till vissa av parameterizations, som vi förklarar här.

## <a name="one-time-setup"></a>Konfiguration vid ett tillfälle
Om du inte har använt PowerShell med din Azure-prenumeration tidigare än:

Installera Azure PowerShell-modulen på den dator där du vill köra skripten:

1. Installera [installations programmet för Microsoft Web Platform (V5 eller högre)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Använd den för att installera Microsoft Azure PowerShell.

Förutom att använda Resource Manager-mallar finns det en omfattande uppsättning [Application Insights PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights), vilket gör det enkelt att konfigurera Application Insights resurser program mässigt. Funktionerna som aktive ras av cmdletarna är:

* Skapa och ta bort Application Insights resurser
* Hämta listor över Application Insights resurser och deras egenskaper
* Skapa och hantera kontinuerlig export
* Skapa och hantera program nycklar
* Ange dagligt tak
* Ange pris Planen

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Skapa Application Insights-resurser med hjälp av en PowerShell-cmdlet

Så här skapar du en ny Application Insights-resurs i Azures Data Center för USA med cmdleten [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Skapa Application Insights resurser med en Resource Manager-mall

Så här skapar du en ny Application Insights resurs med hjälp av en Resource Manager-mall.

### <a name="create-the-azure-resource-manager-template"></a>Skapa Azure Resource Manager-mallen

Skapa en ny. JSON-fil – låt oss anropa den `template1.json` i det här exemplet. Kopiera det här innehållet till det:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Använd Resource Manager-mallen för att skapa en ny Application Insights resurs

1. Logga in på Azure med hjälp av `$Connect-AzAccount` i PowerShell
2. Ange din kontext till en prenumeration med `Set-AzContext "<subscription ID>"`
2. Kör en ny distribution för att skapa en ny Application Insights-resurs:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` är den grupp där du vill skapa de nya resurserna.
   * `-TemplateFile` måste inträffa innan de anpassade parametrarna.
   * `-appName` namnet på den resurs som ska skapas.

Du kan lägga till andra parametrar – du hittar deras beskrivningar i avsnittet parametrar i mallen.

## <a name="get-the-instrumentation-key"></a>Hämta Instrumentation-nyckeln

När du har skapat en program resurs vill du ha Instrumentation-nyckeln: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Om du vill se en lista över många andra egenskaper för din Application Insights-resurs använder du:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Ytterligare egenskaper är tillgängliga via cmdletarna:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Se den [detaljerade dokumentationen](https://docs.microsoft.com/powershell/module/az.applicationinsights) för parametrarna för dessa cmdletar.  

## <a name="set-the-data-retention"></a>Ange data kvarhållning 

Om du vill hämta aktuell datakvarhållning för din Application Insights-resurs kan du använda OSS-verktyget [ARMClient](https://github.com/projectkudu/ARMClient).  (Läs mer om ARMClient från artiklar av [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) och [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Här är ett exempel som använder `ARMClient`för att hämta aktuell kvarhållning:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Om du vill ställa in kvarhållning är kommandot ett liknande sätt:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Om du vill ange data kvarhållning till 365 dagar med mallen ovan kör du:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Följande skript kan även användas för att ändra kvarhållning. Kopiera det här skriptet för att spara som `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Skriptet kan sedan användas som:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Ange dagligt tak

Använd cmdleten [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) för att hämta egenskaperna för dagligt tak: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Använd samma cmdlet för att ange egenskaper för dagligt tak. Om du t. ex. vill ange 300 GB/dag för Cap 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Ange pris Planen 

Använd cmdleten [set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) för att hämta aktuell pris sättnings plan: 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Ange pris Planen genom att använda samma cmdlet med den angivna `-PricingPlan`:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Du kan också ställa in pris Planen för en befintlig Application Insights resurs med hjälp av Resource Manager-mallen ovan, utan att använda resursen "Microsoft. Insights/Components" och noden `dependsOn` från fakturerings resursen. Om du till exempel vill ange den till per GB-plan (tidigare kallat bas planen) kör du:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|projektplan|
|---|---|
|1|Per GB (tidigare kallat Basic-planen)|
|2|Per nod (tidigare namn företags planen)|

## <a name="add-a-metric-alert"></a>Lägg till en måtta avisering

Om du vill ställa in en måtta avisering samtidigt som din app-resurs kan du slå samman kod som detta i mallfilen:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

När du anropar mallen kan du också lägga till den här parametern:

    `-responseTime 2`

Du kan naturligtvis Parameterisera andra fält. 

Du kan ta reda på typ namn och konfigurations information för andra varnings regler genom att skapa en regel manuellt och sedan granska den i [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Lägg till ett tillgänglighets test

Det här exemplet är för ett ping-test (för att testa en enda sida).  

**Det finns två delar** i ett tillgänglighets test: själva testet och aviseringen som meddelar dig om felen.

Sammanfoga följande kod i mallfilen som skapar appen.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Om du vill identifiera koderna för andra test platser, eller automatisera skapandet av mer komplexa webbtester, skapar du ett exempel manuellt och Parameterisera sedan koden från [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Lägg till fler resurser

Om du vill automatisera skapandet av någon annan resurs av någon typ skapar du ett exempel manuellt och kopierar sedan och Parameterisera koden från [Azure Resource Manager](https://resources.azure.com/). 

1. Öppna [Azure Resource Manager](https://resources.azure.com/). Navigera till program resursen genom att gå till `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`. 
   
    ![Navigering i Azure Resource Explorer](./media/powershell/01.png)
   
    *Komponenterna* är de grundläggande Application Insights resurserna för att visa program. Det finns separata resurser för tillhör ande aviserings regler och webb test för tillgänglighet.
2. Kopiera JSON för komponenten till lämplig plats i `template1.json`.
3. Ta bort följande egenskaper:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Öppna avsnitten `webtests` och `alertrules` och kopiera JSON för enskilda objekt till din mall. (Kopiera inte från `webtests` eller `alertrules` noderna: gå till objekten under dem.)
   
    Varje webb test har en associerad aviserings regel, så du måste kopiera båda.
   
    Du kan även inkludera aviseringar för mått. [Mått namn](powershell-alerts.md#metric-names).
5. Infoga den här raden i varje resurs:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parameterisera mallen
Nu måste du ersätta de angivna namnen med parametrar. Om du vill [Parameterisera en mall](../../azure-resource-manager/resource-group-authoring-templates.md)skriver du uttryck med hjälp av en [uppsättning hjälp funktioner](../../azure-resource-manager/resource-group-template-functions.md). 

Du kan inte Parameterisera bara en del av en sträng, så Använd `concat()` för att bygga strängar.

Här följer några exempel på de ersättningar du vill göra. Det finns flera förekomster av varje ersättning. Du kan behöva andra i din mall. I de här exemplen används de parametrar och variabler som vi definierade överst i mallen.

| find | Ersätt med |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (gemen) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Ange beroenden mellan resurserna
Azure bör konfigurera resurserna i strikt ordning. För att se till att en installation är slutförd innan nästa börjar lägger du till beroende linjer:

* I tillgänglighets test resursen:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* I aviserings resursen för ett tillgänglighets test:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Nästa steg
Andra automatiserings artiklar:

* [Skapa en Application Insights resurs](powershell-script-create-resource.md) – snabb metod utan att använda en mall.
* [Konfigurera aviseringar](powershell-alerts.md)
* [Skapa webbtester](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Skicka Azure Diagnostics-data till Application Insights](powershell-azure-diagnostics.md)
* [Distribuera till Azure från GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Skapa versions anteckningar](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
