---
title: Hur du hanterar Azure tid serien Insights miljön med hjälp av Azure Resource Manager-mallar | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar Azure tid serien Insights miljön via programmering med Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 561483b801773b39ed2ff61efa1496eba472530d
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293361"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Skapa tid serien Insights-resurser med hjälp av Azure Resource Manager-mallar

Den här artikeln beskriver hur du skapar och distribuerar tid serien Insights-resurser med Azure Resource Manager-mallar, PowerShell och resursprovidern tid serien insikter.

Tid serien insikter stöder följande resurser:
   | Resurs | Beskrivning |
   | --- | --- |
   | Miljö | En gång serien insikter-miljö är en logisk gruppering av händelser som är läsa från händelsen mäklare, lagras och blir tillgängliga för frågan. Mer information finns i [planera Azure tid serien Insights-miljön](time-series-insights-environment-planning.md) |
   | Händelsekälla | En händelsekälla är en anslutning till en händelse broker från vilken tid serien insikter läser och en händelser i miljön. För närvarande stöds händelsekällor är IoT-hubb och Event Hub. |
   | Referens för datauppsättning | Referensdatamängder ange metadata om händelser i miljön. Metadata i datauppsättningar referens ska anslutas med händelser under ingång. Referensdatamängder definieras av deras viktiga egenskaper för händelse som resurser. De faktiska metadata som utgör datamängden som referens har överförts eller ändras via dataplan API: er. |
   | Åtkomstprincip | Åtkomstprinciper bevilja behörighet att skicka datafrågor, ändra referensdata i miljön och dela sparade frågor och perspektiv som är associerade med miljön. Mer information finns i [bevilja åtkomst till data till en tid serien insikter med hjälp av Azure portal](time-series-insights-data-access.md) |

En mall för hanteraren för filserverresurser är en JSON-fil som definierar infrastrukturen och konfigurationen av resurser i en resursgrupp. Mer information finns i följande dokument:

- [Översikt av Azure Resource Manager - mall för distribution](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

Den [201-timeseriesinsights-miljö-med-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) quickstart mallen har publicerats på GitHub. Den här mallen skapar en tid serien insikter miljö, en underordnad händelsekälla som konfigurerats för att använda händelser från en Händelsehubb och åtkomstprinciper som ger åtkomst till data i miljön. Om en befintlig Händelsehubb har inte angetts, skapas en med distributionen.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuera Snabbstart mallen lokalt med hjälp av PowerShell

Följande procedur beskriver hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar en tid serien insikter miljö, en underordnad händelsekälla som konfigurerats för att använda händelser från en Händelsehubb och åtkomstprinciper som ger åtkomst till den miljödata. Om en befintlig Händelsehubb har inte angetts, skapas en med distributionen.

Ungefärlig arbetsflödet är följande:

1. Installera PowerShell.
1. Skapa mallen och en parameterfil.
1. Logga in på ditt Azure-konto i PowerShell.
1. Skapa en ny resursgrupp om det inte finns.
1. Testa distributionen.
1. Distribuera mallen.

### <a name="install-powershell"></a>Installera PowerShell

Installera Azure PowerShell genom att följa anvisningarna i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Skapa en mall

Klona eller kopiera den [201-timeseriesinsights-miljö-med-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) mall från GitHub.

### <a name="create-a-parameters-file"></a>Skapa en fil med parametrar

Om du vill skapa en fil med parametrar, kopiera den [201-timeseriesinsights-miljö-med-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) fil.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Obligatoriska parametrar

   | Parameter | Beskrivning |
   | --- | --- |
   | eventHubNamespaceName | Namnområdet för händelsehubben källa. |
   | eventHubName | Namnet på händelsehubben källa. |
   | consumerGroupName | Namnet på konsumentgrupp som tid serien Insights-tjänsten använder för att läsa data från event hub. **Obs:** för att undvika resurskonflikter konsumentgruppen dedikerad till tid serien Insights-tjänsten och inte delas med andra läsare. |
   | EnvironmentName | Namnet på miljön. Namnet får inte innehålla: ' <', ' >', '%', 'och', ': ','\\','?', '/' och eventuella kontrolltecken. Alla andra tecken tillåts.|
   | eventSourceName | Namnet på händelsen källa underordnade resursen. Namnet får inte innehålla: ' <', ' >', '%', 'och', ': ','\\','?', '/' och eventuella kontrolltecken. Alla andra tecken tillåts. |

#### <a name="optional-parameters"></a>Valfria parametrar finns:

   | Parameter | Beskrivning |
   | --- | --- |
   | existingEventHubResourceId | En valfri resurs-ID för en befintlig Händelsehubb som ansluts till händelsekällan tid serien insikter miljön. **Obs:** användaren distribuerar mallen måste ha behörighet att utföra åtgärden på den Event Hub listkeys. Om inget värde för skapas en ny händelsehubb av mallen. |
   | environmentDisplayName | Ett valfritt eget namn som ska visas i verktygsuppsättning eller användare gränssnitt i stället för namnet miljö. |
   | environmentSkuName | Namnet på SKU: n. Mer information finns i [tid serien insikter prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Enhet kapacitet SKU: N. Mer information finns i [tid serien insikter prissättning](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Minsta timespan händelser i miljön blir tillgängliga för frågan. Värdet måste anges i ISO 8601-format, till exempel ”P30D” för en bevarandeprincip 30 dagar. |
   | eventSourceDisplayName | Ett valfritt eget namn ska visas i verktygsuppsättning eller användaren gränssnitt i stället för namnet på käll-händelse. |
   | eventSourceTimestampPropertyName | Egenskapen som ska användas som den händelsekälla tidsstämpel. Om ett värde saknas för timestampPropertyName, eller om null eller tom sträng har angetts, kommer tiden för skapandet av händelsen att användas. |
   | eventSourceKeyName | Namnet på den delade åtkomstnyckeln som tid serien Insights-tjänsten använder för att ansluta till händelsehubben. |
   | accessPolicyReaderObjectIds | En lista med objekt-ID för de användare eller program i Azure AD som ska ha läsbehörighet för miljön. Tjänstens huvudnamn objectId kan erhållas genom att anropa den **Get-AzureRMADUser** eller **Get-AzureRMADServicePrincipal** cmdlets. Skapa en åtkomstprincip för Azure AD-grupper stöds inte ännu. |
   | accessPolicyContributorObjectIds | En lista med objekt-ID för de användare eller program i Azure AD som ska ha deltagare åtkomst till miljön. Tjänstens huvudnamn objectId kan erhållas genom att anropa den **Get-AzureRMADUser** eller **Get-AzureRMADServicePrincipal** cmdlets. Skapa en åtkomstprincip för Azure AD-grupper stöds inte ännu. |

Exempelvis ska följande parameterfilen användas för att skapa en miljö och en händelsekälla som läser händelser från en befintlig händelsehubb. Dessutom skapas två principer för åtkomst som beviljar åtkomst deltagare i miljön.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Mer information finns i [parametrar](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artikel.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logga in på Azure och ange Azure-prenumeration

Kör följande kommando från en PowerShell-kommandotolk:

```powershell
Connect-AzureRmAccount
```

Du uppmanas att logga in på ditt Azure-konto. Kör följande kommando för att visa tillgängliga prenumerationer efter inloggningen:

```powershell
Get-AzureRMSubscription
```

Det här kommandot returnerar en lista över tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för Azure-prenumerationen du vill använda:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ange resursgruppen.

Om du inte har en befintlig resurs, skapa en ny resursgrupp med det **New-AzureRmResourceGroup** kommando. Ange namnet på den resursgrupp och plats som du vill använda. Exempel:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Om detta lyckas visas en sammanfattning av den nya resursgruppen.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testa distributionen

Verifiera distributionen genom att köra den `Test-AzureRmResourceGroupDeployment` cmdlet. När du testar distributionen, ange parametrar precis som vid körning av distributionen.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Skapa en distribution

Om du vill skapa den nya distributionen, kör den `New-AzureRmResourceGroupDeployment` cmdlet, och ange nödvändiga parametrar när du tillfrågas. Parametrarna inkluderar ett namn för din distribution, namnet på din resursgrupp och sökväg eller URL till mallfilen. Om den **läge** parametern anges standardvärdet **stegvis** används. Mer information finns i [inkrementell och fullständig distributioner](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Följande kommando efterfrågar fem nödvändiga parametrar i PowerShell-fönstret:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Om du vill ange en fil med parametrar i stället använder du följande kommando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Du kan också använda infogade parametrar när du kör cmdlet för distribution. Kommandot är följande:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Att köra en [fullständig](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) distribution, ange den **läge** parameter till **Slutför**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Kontrollera distributionen

Om resurserna som har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Distribuera Snabbstart mallen via Azure portal

Mallen quickstart startsidan på GitHub innehåller också en **till Azure** knappen. Om du klickar på den öppnas en anpassad distribution sida i Azure-portalen. Den här sidan kan du ange eller Välj värden för var och en av parametrarna från den [obligatoriska parametrar](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) eller [valfria parametrar](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabeller. När du fyller i inställningar, klicka på **inköp** knappen initierar mallen distribueras.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar tid serien Insights-resurser med hjälp av REST API: er via programmering finns [tidshantering serien insikter](https://docs.microsoft.com/rest/api/time-series-insights-management/).
