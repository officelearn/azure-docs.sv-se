---
title: Automatisera Azure Application Insights med PowerShell | Microsoft Docs
description: Automatisera skapande resurs, avisering och tillgänglighet tester i PowerShell med hjälp av en Azure Resource Manager-mall.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 4b633f3294faf11c8ef9d4a4077254c3df5353cf
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264861"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Skapa Application Insights-resurser med hjälp av PowerShell
Den här artikeln visar hur du automatiskt skapa och uppdatera [Application Insights](../../azure-monitor/app/app-insights-overview.md) resurser automatiskt med hjälp av Azure Resource Manager. Du kan till exempel göra det som en del av en build-process. Tillsammans med grundläggande Application Insights-resursen kan du skapa [webbtester för tillgänglighet](../../azure-monitor/app/monitor-web-app-availability.md), Ställ in [aviseringar](../../azure-monitor/app/alerts.md), ange den [priser schema](pricing.md), och skapa andra Azure-resurser .

Nyckeln till att skapa dessa resurser är JSON-mallar för [Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md). Kortfattat, proceduren är: ladda ned JSON-definitioner av befintliga resurser. Parameterisera vissa värden, till exempel namn. och kör sedan mallen när du vill skapa en ny resurs. Du kan paketera flera resurser tillsammans, skapa dem på en go - exempel: en app Övervakare med tillgänglighetstester, aviseringar och lagring för löpande export. Det finns vissa nyanser till vissa av parameterizations som beskrivs här.

## <a name="one-time-setup"></a>Konfigurationen gång
Om du inte har använt PowerShell med Azure-prenumerationen innan du:

Installera Azure Powershell-modulen på datorn där du vill köra skripten:

1. Installera [Microsoft Web Platform Installer (v5 eller högre)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Du kan använda den för att installera Microsoft Azure Powershell.

## <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall
Skapa en ny .json-fil – vi kan kalla den `template1.json` i det här exemplet. Kopiera det här innehållet till den:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
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
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
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
                    "description": "1 = Basic, 2 = Enterprise"
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



## <a name="create-application-insights-resources"></a>Skapa Application Insights-resurser
1. Logga in på Azure i PowerShell:
   
    `Connect-AzureRmAccount`
2. Kör ett kommando som detta:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` är gruppen där du vill skapa nya resurser.
   * `-TemplateFile` måste slutföras innan de anpassade parametrarna.
   * `-appName` Namnet på resursen som ska skapas.

Du kan lägga till andra parametrar – hittar du deras beskrivningar i avsnittet parametrar i mallen.

## <a name="to-get-the-instrumentation-key"></a>Att hämta instrumenteringsnyckeln
När du har skapat en resurs för en du instrumenteringsnyckeln: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Ange prisplan

Du kan ange den [prisplan](pricing.md).

Skapa en app-resurs med pris företagsplanen, med hjälp av mallen ovan:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|plan|
|---|---|
|1|Basic|
|2|Enterprise|

* Om du endast vill använda standard Basic-prisplan kan du utelämna CurrentBillingFeatures resursen från mallen.
* Om du vill ändra prisplanen när komponenten resursen har skapats kan du använda en mall som utesluter ”microsoft.insights/components”-resurs. Dessutom utelämna den `dependsOn` nod från fakturering resursen. 

Kontrollera de uppdaterade prisplanen genom att titta på den **användning och uppskattade kostnader** bladet i webbläsaren. **Uppdatera visningen av webbläsaren** att kontrollera att du ser det aktuella tillståndet.



## <a name="add-a-metric-alert"></a>Lägg till en måttavisering

Om du vill konfigurera en metrisk varning samtidigt som din app-resurs, sammanfoga kod som denna i mallfilen:

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

När du anropar mallen kan du lägga till den här parametern:

    `-responseTime 2`

Du kan naturligtvis Parameterisera andra fält. 

Om du vill ta reda på namn och information om konfiguration av andra Varningsregler, skapa en regel manuellt och sedan granska den i [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Lägg till ett tillgänglighetstest

Det här exemplet är för ett Pingtest (att testa en enda sida).  

**Det finns två delar** i ett tillgänglighetstest: testning själv och den avisering som meddelar dig om fel.

Sammanfoga följande kod till mallfilen som skapar appen.

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

Identifiera koder för andra testplatser eller för att automatisera skapandet av webbtester för mer komplexa, skapa ett exempel manuellt och sedan Parameterisera koden från [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Lägga till fler resurser

Om du vill automatisera skapandet av någon annan resurs av något slag, skapa ett exempel manuellt, och kopierar samt Parameterisera koden från [Azure Resource Manager](https://resources.azure.com/). 

1. Öppna [med Azure Resource Manager](https://resources.azure.com/). Gå nedåt via `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, till din programresurs. 
   
    ![Navigering i Azure Resource Explorer](./media/powershell/01.png)
   
    *Komponenter* är de grundläggande Application Insights-resurserna för att visa program. Det finns separata resurser för den associerade Varningsregler och webbtester för tillgänglighet.
2. Kopiera JSON för komponenten till lämplig plats i `template1.json`.
3. Ta bort de här egenskaperna:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Öppna avsnitten webbtester och alertrules och kopiera JSON för enskilda objekt i mallen. (Inte kopiera från webbtester eller alertrules noder: Gå till objekt därunder.)
   
    Varje webbtest har en associerad aviseringsregel så du måste kopiera båda.
   
    Du kan även inkludera aviseringar i mått. [Tjänstmåttets namn](powershell-alerts.md#metric-names).
5. Infoga den här raden i varje resurs:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parameterisera mallen
Nu har du ersätta specifika namn med parametrar. Att [Parameterisera en mall](../../azure-resource-manager/resource-group-authoring-templates.md), du kan skriva uttryck med hjälp av en [uppsättning Hjälpfunktioner](../../azure-resource-manager/resource-group-template-functions.md). 

Du kan inte Parameterisera bara en del av en sträng, så Använd `concat()` att skapa strängar.

Här följer exempel på med ändringar som du vill se. Det finns flera förekomster av varje ersättningen. Du kanske behöver andra i din mall. De här exemplen använder parametrar och variabler som vi definierade överst i mallen.

| find | Ersätt med |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (gemen) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Ta bort Guid och -Id. |

### <a name="set-dependencies-between-the-resources"></a>Ange beroenden mellan resurser
Azure bör ställa in resurserna i strikt ordning. För att säkerställa en installationen är klar innan nästa påbörjas, lägger du till beroende rader:

* Testa resurs i tillgängligheten:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* I aviseringen resurs för ett tillgänglighetstest:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Nästa steg
Andra automation-artiklar:

* [Skapa en Application Insights-resurs](powershell-script-create-resource.md) -snabb metod utan att använda en mall.
* [Ställa in aviseringar](powershell-alerts.md)
* [Skapa webbtester](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Skicka Azure Diagnostics-data till Application Insights](powershell-azure-diagnostics.md)
* [Distribuera till Azure från GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Skapa Versionsanteckningar](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

