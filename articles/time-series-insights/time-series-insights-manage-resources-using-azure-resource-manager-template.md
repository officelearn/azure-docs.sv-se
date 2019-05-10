---
title: Hur du hanterar din Azure Time Series Insights-miljö med Azure Resource Manager-mallar | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar din Azure Time Series Insights-miljö via programmering med Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: f5e350e8a9093936f1e747afda7c3192b4d8368d
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471716"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Skapa Time Series Insights-resurser med Azure Resource Manager-mallar

Den här artikeln beskriver hur du skapar och distribuerar Time Series Insights-resurser med hjälp av Azure Resource Manager-mallar, PowerShell och Time Series Insights-resursprovidern.

Time Series Insights har stöd för följande resurser:

   | Resource | Beskrivning |
   | --- | --- |
   | Miljö | En Time Series Insights-miljö är en logisk gruppering av händelser som läses från asynkrona meddelandeköer för händelser, lagras, och blir tillgängliga för frågor. Mer information finns i [planera Azure Time Series Insights-miljön](time-series-insights-environment-planning.md) |
   | Händelsekälla | En händelsekälla är en anslutning till en asynkron meddelandekö för händelser som Time Series Insights läser och matar in händelser i miljön. För närvarande stöds händelsekällor är IoT Hub och Event Hub. |
   | Referensdatauppsättning | Referensdatauppsättningar finns metadata om händelser i miljön. Metadata i referensdatauppsättningar kommer att anslutas med händelser under ingående. Referensdatauppsättningar definieras som resurser av deras viktiga egenskaper för händelse. De faktiska metadata som utgör referensdatauppsättningen laddas upp till eller ändras via dataplanet API: er. |
   | Åtkomstprincip | Åtkomstprinciper bevilja behörigheter för att utfärda datafrågor, manipulera referensdata i miljön och dela sparade frågor och perspektiv som är associerade till miljön. Mer information finns [bevilja åtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md) |

En Resource Manager-mall är en JSON-fil som definierar infrastruktur och konfiguration av resurser i en resursgrupp. Följande dokument beskriver mallfiler i större detalj:

- [Översikt av Azure Resource Manager - malldistribution](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Microsoft.TimeSeriesInsights resurstyper](/azure/templates/microsoft.timeseriesinsights/allversions)

Den [201-timeseriesinsights-miljö-med-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) snabbstartsmall publiceras på GitHub. Den här mallen skapar en Time Series Insights-miljö, en underordnad händelsekällan som konfigurerats för att konsumera händelser från en Event Hub och åtkomstprinciper som beviljar åtkomst till den miljön data. Om en befintlig Händelsehubb har inte angetts, skapas en med distributionen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Ange Distributionsmall och parametrar

Följande procedur beskriver hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar en Time Series Insights-miljö, en underordnad händelsekällan som konfigurerats för att konsumera händelser från en Event Hub och åtkomstprinciper som ger åtkomst till den miljödata. Om en befintlig Händelsehubb har inte angetts, skapas en med distributionen.

1. Installera Azure PowerShell genom att följa instruktionerna i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

1. Klona eller kopiera den [201-timeseriesinsights-miljö-med-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) mall från GitHub.

   * Skapa en fil med parametrar

     Om du vill skapa en fil med parametrar, kopiera den [201-timeseriesinsights-miljö-med-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fil.

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Obligatoriska parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | eventHubNamespaceName | Namnområdet för händelsehubben källa. |
     | eventHubName | Namnet på händelsehubben källa. |
     | consumerGroupName | Namnet på konsumentgrupp som ska användas av Time Series Insights-tjänsten för att läsa data från händelsehubben. **Obs!** För att undvika resurskonflikter kan måste den här konsumentgruppen vara dedikerad till Time Series Insights-tjänsten och inte delas med andra läsare. |
     | EnvironmentName | Namnet på miljön. Namnet får inte innehålla: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, och något kontrolltecken. Alla andra tecken tillåts.|
     | eventSourceName | Namnet på händelsen källa underordnade resursen. Namnet får inte innehålla: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/`, och något kontrolltecken. Alla andra tecken tillåts. |

    <div id="optional-parameters"></div>

   * Valfria parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | existingEventHubResourceId | En valfri resurs-ID för en befintlig Händelsehubb som ska anslutas till Time Series Insights-miljö via händelsekällan. **Obs!** Du distribuerar mallen måste ha behörighet att utföra åtgärden listnycklar i Event Hub. Om inget värde skickas, skapas en ny händelsehubb av mallen. |
     | environmentDisplayName | Ett valfritt eget namn ska visas i verktyg eller användaren gränssnitt i stället för miljönamn. |
     | environmentSkuName | Namnet på SKU:n. Mer information finns i den [prissättning för Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | Enhet kapaciteten för SKU: N. Mer information finns i den [prissättning för Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | Minsta timespan miljöns händelser blir tillgängliga för frågor. Värdet måste anges i ISO 8601-format, till exempel `P30D` för en bevarandeprincip på 30 dagar. |
     | eventSourceDisplayName | Ett valfritt eget namn ska visas i verktyg eller användaren gränssnitt i stället för händelsekällans namn. |
     | eventSourceTimestampPropertyName | Händelseegenskapen som ska användas som den händelsekälla tidsstämpel. Om du inte anger något värde för timestampPropertyName, eller om null eller tom sträng har angetts, kommer tiden för skapandet av händelsen att användas. |
     | eventSourceKeyName | Namnet på den delade åtkomstnyckeln som ska användas av Time Series Insights-tjänsten för att ansluta till event hub. |
     | accessPolicyReaderObjectIds | En lista med objekt-ID för de användare eller program i Azure AD som ska ha läsåtkomst till miljön. Tjänstens huvudnamn objectId kan hämtas genom att anropa den **Get-AzADUser** eller **Get-AzADServicePrincipal** cmdletar. Skapa en åtkomstprincip för Azure AD-grupper stöds inte ännu. |
     | accessPolicyContributorObjectIds | En lista med objekt-ID för de användare eller program i Azure AD som ska ha deltagaråtkomst till miljön. Tjänstens huvudnamn objectId kan hämtas genom att anropa den **Get-AzADUser** eller **Get-AzADServicePrincipal** cmdletar. Skapa en åtkomstprincip för Azure AD-grupper stöds inte ännu. |

   * Till exempel skulle följande parameterfilen användas för att skapa en miljö och en händelsekälla som läser händelser från en befintlig händelsehubb. Det skapar också två åtkomstprinciper som beviljar deltagaråtkomst till miljön.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```
  
    * Mer information finns i den [parametrar](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artikeln.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuera snabbstartsmall lokalt med hjälp av PowerShell

> [!IMPORTANT]
> Kommandoradsåtgärder som visas nedan beskrivs de [Az PowerShell-modulen](https://docs.microsoft.com/powershell/azure/overview).

1. Logga in på ditt Azure-konto i PowerShell.

    * Kör följande kommando från en PowerShell-kommandotolk:

      ```powershell
      Connect-AzAccount
      ```

    * Du uppmanas att logga in på ditt Azure-konto. Efter inloggningen, kör du följande kommando för att visa dina tillgängliga prenumerationer:

      ```powershell
      Get-AzSubscription
      ```

    * Det här kommandot returnerar en lista över tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för Azure-prenumeration du vill använda:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Skapa en ny resursgrupp om det inte finns.

   * Om du inte har en befintlig resurs, skapa en ny resursgrupp med det **New AzResourceGroup** kommando. Ange namnet på resursgruppen och den plats som du vill använda. Exempel:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Om detta lyckas visas en sammanfattning av den nya resursgruppen.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Testa distributionen.

   * Verifiera distributionen genom att köra den `Test-AzResourceGroupDeployment` cmdlet. När du testar distributionen kan du ange parametrar, precis som när du genomför distributionen.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Skapa distributionen

    * Du skapar den nya distributionen måste köra den `New-AzResourceGroupDeployment` cmdlet, och ange nödvändiga parametrar när du tillfrågas. Parametrarna inkluderar ett namn för din distribution, namnet på resursgruppen, och sökvägen eller URL: en till mallfilen. Om den **läge** parametern inte anges standardvärdet för **stegvis** används. Mer information finns i [inkrementell och fullständig distributioner](../azure-resource-manager/deployment-modes.md).

    * Kommandot frågar efter fem obligatoriska parametrar i PowerShell-fönstret:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Om du vill ange en fil med parametrar i stället använder du följande kommando:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Du kan också använda infogade parametrar när du kör cmdlet för distribution. Kommandot är följande:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Att köra en [fullständig](../azure-resource-manager/deployment-modes.md) distributionen, den **läge** parameter **Slutför**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verifiera distributionen

    * Om resurserna som har distribuerats, visas en sammanfattning av distributionen i PowerShell-fönstret:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 5/8/2019 10:28:34 PM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Distribuera snabbstartsmall via Azure portal

   * Den snabbstartsmall startsidan på GitHub omfattar även en **distribuera till Azure** knappen. Att klicka på den öppnas en sida för anpassad distribution i Azure-portalen. Den här sidan kan du ange eller Välj värden för var och en av parametrarna från den [obligatoriska parametrar](#required-parameters) eller [valfria parametrar](#optional-parameters) tabeller. När du har fyllt i inställningar, klicka på **köp** knappen initierar malldistributionen.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du programmässigt hantera Time Series Insights-resurser med hjälp av REST API: er finns i [Time Series Insights-hantering](https://docs.microsoft.com/rest/api/time-series-insights-management/).
