---
title: Automatisera Azure Application Insights med PowerShell | Microsoft-dokument
description: Automatisera skapande och hantering av resurser, aviseringar och tillgänglighetstester i PowerShell med hjälp av en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275885"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Hantera resurser för programinsikter med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln visar hur du automatiserar skapandet och uppdateringen av [Application Insights-resurser](../../azure-monitor/app/app-insights-overview.md) automatiskt med hjälp av Azure Resource Management. Du kan till exempel göra det som en del av en byggprocess. Tillsammans med den grundläggande Application Insights-resursen kan du skapa [tillgänglighetswebbtester,](../../azure-monitor/app/monitor-web-app-availability.md)ställa in [aviseringar,](../../azure-monitor/app/alerts.md)ange [prisschema](pricing.md)och skapa andra Azure-resurser.

Nyckeln till att skapa dessa resurser är JSON-mallar för [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). Det grundläggande tillvägagångssättet är: ladda ner JSON-definitionerna av befintliga resurser; parameterisera vissa värden, till exempel namn. och kör sedan mallen när du vill skapa en ny resurs. Du kan paketera flera resurser tillsammans för att skapa dem på en gång , till exempel en appövervakare med tillgänglighetstester, aviseringar och lagring för kontinuerlig export. Det finns några nyanser till några av parameteriseringarna, som vi ska förklara här.

## <a name="one-time-setup"></a>Engångsinställningar
Om du inte har använt PowerShell med din Azure-prenumeration tidigare:

Installera Azure Powershell-modulen på datorn där du vill köra skripten:

1. Installera [Installationsprogrammet för Microsoft Web Platform (v5 eller senare)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Använd den för att installera Microsoft Azure Powershell.

Förutom att använda Resource Manager-mallar finns det en omfattande uppsättning [Application Insights PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights), vilket gör det enkelt att konfigurera Application Insights-resurser programmässigt. De funktioner som aktiveras av cmdlets inkluderar:

* Skapa och ta bort application insights-resurser
* Hämta listor över Application Insights-resurser och deras egenskaper
* Skapa och hantera kontinuerlig export
* Skapa och hantera programnycklar
* Ställ in den dagliga tak
* Ange prisplanen

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Skapa resurser för programstatistik med en PowerShell-cmdlet

Så här skapar du en ny Application Insights-resurs i Azure East US-datacentret med cmdleten [New-AzApplicationInsights:](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights)

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Skapa resurser för programstatistik med hjälp av en Resource Manager-mall

Så här skapar du en ny Application Insights-resurs med hjälp av en Resource Manager-mall.

### <a name="create-the-azure-resource-manager-template"></a>Skapa Azure Resource Manager-mallen

Skapa en ny .json-fil – `template1.json` låt oss kalla det i det här exemplet. Kopiera det här innehållet till det:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Använda resurshanterarens mall för att skapa en ny Application Insights-resurs

1. Logga in på Azure i PowerShell med`$Connect-AzAccount`
2. Ange din kontext till en prenumeration med`Set-AzContext "<subscription ID>"`
2. Kör en ny distribution för att skapa en ny Application Insights-resurs:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`är den grupp där du vill skapa de nya resurserna.
   * `-TemplateFile`måste inträffa före de anpassade parametrarna.
   * `-appName`Namnet på den resurs som ska skapas.

Du kan lägga till andra parametrar - du hittar deras beskrivningar i parameteravsnittet i mallen.

## <a name="get-the-instrumentation-key"></a>Hämta instrumenteringsnyckeln

När du har skapat en programresurs vill du ha instrumenteringsnyckeln: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Om du vill visa en lista över många andra egenskaper för application insights-resursen använder du:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Ytterligare egenskaper finns tillgängliga via cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Se detaljerad [dokumentation](https://docs.microsoft.com/powershell/module/az.applicationinsights) för parametrarna för dessa cmdlets.  

## <a name="set-the-data-retention"></a>Ange datalagring 

Om du vill hämta den aktuella datalagringen för din Application Insights-resurs kan du använda OSS-verktyget [ARMClient](https://github.com/projectkudu/ARMClient).  (Läs mer om ARMClient från artiklar av [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) och [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Här är ett `ARMClient`exempel med , för att få den aktuella kvarhållningen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Om du vill ställa in kvarhållningen är kommandot ett liknande PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Om du vill ställa in datalagringen till 365 dagar med mallen ovan kör du:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Följande skript kan också användas för att ändra kvarhållning. Kopiera skriptet för `Set-ApplicationInsightsRetention.ps1`att spara som .

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

Det här skriptet kan sedan användas som:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Ställ in det dagliga locket

För att få de dagliga cap-egenskaperna använder du [cmdlet set-azapplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Använd samma cmdlet för att ställa in de dagliga lockegenskaperna. Om du till exempel vill ställa in taket på 300 GB/dag

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Du kan också använda [ARMClient](https://github.com/projectkudu/ARMClient) för att hämta och ställa in dagliga takparametrar.  Om du vill hämta de aktuella värdena använder du:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Ställ in den dagliga återställningstiden för locket

Om du vill ställa in den dagliga återställningstiden för locket kan du använda [ARMClient](https://github.com/projectkudu/ARMClient). Här är ett `ARMClient`exempel med , för att ställa in återställningstiden till en ny timme (i det här exemplet 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Ange prisplan 

Om du vill hämta aktuell prisplan använder du [cmdlet set-azapplicationInsightsPricingPlan:](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan)

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Om du vill ställa in prisplanen `-PricingPlan` använder du samma cmdlet med den angivna:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Du kan också ange prisplanen för en befintlig Application Insights-resurs med hjälp av resurshanterarens mall `dependsOn` ovan, genom att utelämna resursen "microsoft.insights/components" och noden från faktureringsresursen. Om du till exempel vill ange den på per GB-planen (tidigare kallad grundplan) kör du:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

Definieras `priceCode` som:

|prisKoda|planera|
|---|---|
|1|Per GB (tidigare kallad Basic-planen)|
|2|Per nod (tidigare namn på Enterprise-planen)|

Slutligen kan du använda [ARMClient](https://github.com/projectkudu/ARMClient) för att få och ställa in prisplaner och dagliga takparametrar.  Om du vill hämta de aktuella värdena använder du:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

Och du kan ställa in alla dessa parametrar med hjälp av:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Detta ställer in det dagliga taket till 200 GB/dag, konfigurerar den dagliga cap reset-tiden till 12:00 UTC, skickar e-postmeddelanden både när locket träffas och varningsnivån är uppfyllda och ställer in varningströskeln till 90% av taket.  

## <a name="add-a-metric-alert"></a>Lägga till en måttavisering

Om du vill automatisera skapandet av måttaviseringar läser du [mallartikeln för måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Lägga till ett tillgänglighetstest

Om du vill automatisera tillgänglighetstester läser du [mallartikeln för måttaviseringar](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Lägga till fler resurser

Om du vill automatisera skapandet av andra resurser av något slag skapar du ett exempel manuellt och kopierar och parameteriserar sedan dess kod från [Azure Resource Manager](https://resources.azure.com/). 

1. Öppna [Azure Resource Manager](https://resources.azure.com/). Navigera nedåt `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`till programresursen. 
   
    ![Navigering i Utforskaren för Azure Resource](./media/powershell/01.png)
   
    *Komponenter* är de grundläggande Application Insights-resurserna för att visa program. Det finns separata resurser för tillhörande aviseringsregler och tillgänglighetswebbtester.
2. Kopiera komponentens JSON till lämplig `template1.json`plats i .
3. Ta bort dessa egenskaper:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Öppna `webtests` avsnitten och `alertrules` kopiera JSON för enskilda objekt i mallen. (Kopiera inte från `webtests` eller `alertrules` noderna: gå in i objekten under dem.)
   
    Varje webbtest har en associerad varningsregel, så du måste kopiera dem båda.
   
    Du kan också inkludera aviseringar om mått. [Måttnamn](powershell-alerts.md#metric-names).
5. Infoga den här raden i varje resurs:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrin i mallen
Nu måste du ersätta de specifika namnen med parametrar. Om du vill [parametriera en mall](../../azure-resource-manager/templates/template-syntax.md)skriver du uttryck med hjälp av en [uppsättning hjälpfunktioner](../../azure-resource-manager/templates/template-functions.md). 

Du kan inte parameterisera bara en del `concat()` av en sträng, så använd för att skapa strängar.

Här är exempel på de ersättningar du vill göra. Det finns flera förekomster av varje ersättning. Du kan behöva andra i mallen. Dessa exempel använder de parametrar och variabler som vi definierade högst upp i mallen.

| find | ersätta med |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(gemener) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Ange beroenden mellan resurserna
Azure bör ställa in resurserna i strikt ordning. Om du vill vara säker på att en inställning har slutförts innan nästa börjar lägger du till beroenderader:

* I testresursen för tillgänglighet:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* I aviseringsresursen för ett tillgänglighetstest:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Nästa steg
Andra automationsartiklar:

* [Skapa en Application Insights-resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - snabb metod utan att använda en mall.
* [Ställ in aviseringar](powershell-alerts.md)
* [Skapa webbtester](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Skicka Azure Diagnostics-data till Application Insights](powershell-azure-diagnostics.md)
* [Distribuera till Azure från GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Skapa versionsanteckningar](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)