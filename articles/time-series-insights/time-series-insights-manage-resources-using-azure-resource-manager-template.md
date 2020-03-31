---
title: Hantera din miljö med Azure Resource Manager-mallar – Azure Time Series Insights | Microsoft-dokument
description: Lär dig hur du hanterar din Azure Time Series Insights-miljö programmässigt med Hjälp av Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1caa80469504d52d3103fb2776fb3e7210971690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024406"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Skapa time series insights-resurser med Azure Resource Manager-mallar

I den här artikeln beskrivs hur du skapar och distribuerar Time Series Insights-resurser med hjälp av [Azure Resource Manager-mallar,](https://docs.microsoft.com/azure/azure-resource-manager/)PowerShell och time series Insights-resursprovidern.

Time Series Insights stöder följande resurser:

   | Resurs | Beskrivning |
   | --- | --- |
   | Miljö | En Time Series Insights-miljö är en logisk gruppering av händelser som läss från händelsemäklare, lagras och görs tillgängliga för frågor. Mer information finns i [Planera din Azure Time Series Insights-miljö](time-series-insights-environment-planning.md) |
   | Händelsekälla | En händelsekälla är en anslutning till en händelsemäklare från vilken Time Series Insights läser och intar händelser i miljön. Händelsekällor som för närvarande stöds är IoT Hub och Event Hub. |
   | Referensdatauppsättning | Referensdatauppsättningar tillhandahåller metadata om händelserna i miljön. Metadata i referensdatauppsättningarna kommer att sammanfogas med händelser under inträngning. Referensdatauppsättningar definieras som resurser av deras händelsenyckelegenskaper. De faktiska metadata som utgör referensdatauppsättningen överförs eller ändras via dataplans-API:er. |
   | Åtkomstprincip | Åtkomstprinciper ger behörighet att utfärda datafrågor, manipulera referensdata i miljön och dela sparade frågor och perspektiv som är associerade med miljön. Mer information finns i [Bevilja dataåtkomst till en Time Series Insights-miljö med Azure-portalen](time-series-insights-data-access.md) |

En Resource Manager-mall är en JSON-fil som definierar infrastruktur och konfiguration av resurser i en resursgrupp. Följande dokument beskriver mallfiler mer i detalj:

- [Azure Resource Manager-malldistribution](../azure-resource-manager/templates/overview.md)
- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Microsoft.TimeSeriesInsights resurstyper](/azure/templates/microsoft.timeseriesinsights/allversions)

Snabbstartsmallen [för 201-timeseriesinsights-environment-with-eventhub publiceras](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) på GitHub. Den här mallen skapar en Time Series Insights-miljö, en underordnad händelsekälla som konfigurerats för att använda händelser från en händelsehubb och åtkomstprinciper som ger åtkomst till miljöns data. Om en befintlig händelsehubb inte har angetts skapas en med distributionen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Ange distributionsmall och parametrar

I följande procedur beskrivs hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar en Time Series Insights-miljö, en underordnad händelsekälla som konfigurerats för att använda händelser från en händelsehubb och åtkomstprinciper som ger åtkomst till miljöns data. Om en befintlig händelsehubb inte har angetts skapas en med distributionen.

1. Installera Azure PowerShell genom att följa instruktionerna i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Klona eller kopiera [201-timeseriesinsights-environment-with-eventhub-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) från GitHub.

   * Skapa en parameterfil

     Om du vill skapa en parameterfil kopierar du [filen 201-timeseriesinsights-environment-with-eventhub.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Obligatoriska parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | eventHubNamespaceName | Namnområdet för källhändelsehubben. |
     | eventHubName | Namnet på källhändelsehubben. |
     | consumerGroupName (konsumentGroupName) | Namnet på den konsumentgrupp som tjänsten Time Series Insights använder för att läsa data från händelsehubben. **OBS:** För att undvika resurskonkurrens måste den här konsumentgruppen vara dedikerad till tjänsten Time Series Insights och inte delas med andra läsare. |
     | miljöNamn | Namnet på miljön. Namnet får inte `<` `>`innehålla: , `\\` `?`, `/` `%`, `&` `:`, , , , och några kontrolltecken. Alla andra tecken tillåts.|
     | händelseKällaNamn | Namnet på den underordnade resursen för händelsekällan. Namnet får inte `<` `>`innehålla: , `\\` `?`, `/` `%`, `&` `:`, , , , och några kontrolltecken. Alla andra tecken tillåts. |

    <div id="optional-parameters"></div>

   * Valfria parametrar

     | Parameter | Beskrivning |
     | --- | --- |
     | befintligaEventHubResourceId | Ett valfritt resurs-ID för en befintlig händelsehubb som ska anslutas till time series insights-miljön via händelsekällan. **OBS:** Användaren som distribuerar mallen måste ha behörighet för att utföra listnycklarna på händelsehubben. Om inget värde skickas skapas en ny händelsehubb av mallen. |
     | miljöDisplayName | Ett valfritt eget namn som ska visas i verktygs- eller användargränssnitt i stället för miljönamnet. |
     | miljöSkuName | Namnet på SKU:n. Mer information finns på [sidan Prissättning av tidsseriestatistik](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | miljöSkuKapacitet | Sku:s enhetskapacitet. Mer information finns på [sidan Prissättning av tidsseriestatistik](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | miljöDataRetentionTime | Minsta tidsrymd som miljöns händelser är tillgängliga för frågor. Värdet måste anges i ISO 8601-formatet, `P30D` till exempel för en bevarandeprincip på 30 dagar. |
     | eventSourceDisplayName | Ett valfritt eget namn som ska visas i verktygs- eller användargränssnitt i stället för händelsekällans namn. |
     | eventSourceTimestampPropertyName | Händelseegenskapen som ska användas som händelsekällans tidsstämpel. Om ett värde inte anges för tidsstämpelPropertyName, eller om null eller tom sträng har angetts, används händelseskapandetiden. |
     | händelseKällaKeyName | Namnet på den delade åtkomstnyckel som tjänsten Time Series Insights använder för att ansluta till händelsehubben. |
     | accessPolicyReaderObjectIds | En lista över objekt-ID:er för användare eller program i Azure AD som ska ha reader-åtkomst till miljön. Tjänstens huvudobjektId kan erhållas genom att anropa **Get-AzADUser** eller Cmdlets **Get-AzADServicePrincipal.** Det går ännu inte att skapa en åtkomstprincip för Azure AD-grupper. |
     | accessPolicyContributorObjectIds | En lista över objekt-ID:er för användare eller program i Azure AD som ska ha deltagareåtkomst till miljön. Tjänstens huvudobjektId kan erhållas genom att anropa **Get-AzADUser** eller Cmdlets **Get-AzADServicePrincipal.** Det går ännu inte att skapa en åtkomstprincip för Azure AD-grupper. |

   * Som ett exempel skulle följande parameterfil användas för att skapa en miljö och en händelsekälla som läser händelser från en befintlig händelsehubb. Det skapar också två åtkomstprinciper som ger Deltagare åtkomst till miljön.

     ```JSON
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

    * Mer information finns i artikeln [Parametrar.](../azure-resource-manager/templates/parameter-files.md)

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuera snabbstartsmallen lokalt med PowerShell

> [!IMPORTANT]
> Kommandoradsåtgärderna som visas nedan beskriver [Az PowerShell-modulen](https://docs.microsoft.com/powershell/azure/overview).

1. Logga in på ditt Azure-konto i PowerShell.

    * Kör följande kommando från en PowerShell-prompt:

      ```powershell
      Connect-AzAccount
      ```

    * Du uppmanas att logga in på ditt Azure-konto. När du har loggat in kör du följande kommando för att visa dina tillgängliga prenumerationer:

      ```powershell
      Get-AzSubscription
      ```

    * Det här kommandot returnerar en lista över tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för den Azure-prenumeration som du vill använda:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Skapa en ny resursgrupp om det inte finns någon.

   * Om du inte har en befintlig resursgrupp skapar du en ny resursgrupp med kommandot **New-AzResourceGroup.** Ange namnet på den resursgrupp och plats som du vill använda. Ett exempel:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Om det lyckas visas en sammanfattning av den nya resursgruppen.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Testa distributionen.

   * Verifiera distributionen genom `Test-AzResourceGroupDeployment` att köra cmdleten. När du testar distributionen anger du parametrar precis som när du kör distributionen.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Skapa distributionen

    * Om du vill skapa `New-AzResourceGroupDeployment` den nya distributionen kör du cmdleten och anger nödvändiga parametrar när du uppmanas att göra det. Parametrarna innehåller ett namn för distributionen, namnet på resursgruppen och sökvägen eller URL:en till mallfilen. Om parametern **Läge** inte har angetts används standardvärdet **för Inkrementellt.** Mer information finns [i Inkrementella och fullständiga distributioner](../azure-resource-manager/templates/deployment-modes.md).

    * Följande kommandotolkar dig för de fem obligatoriska parametrarna i PowerShell-fönstret:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    * Om du vill ange en parameterfil i stället använder du följande kommando:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Du kan också använda infogade parametrar när du kör distributions-cmdlet. Kommandot är följande:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Om du vill köra en [fullständig](../azure-resource-manager/templates/deployment-modes.md) distribution ställer du in parametern **Läge** till **Slutför:**

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verifiera distributionen

    * Om resurserna har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
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

1. Distribuera snabbstartsmallen via Azure-portalen

   * Snabbstartsmallens startsida på GitHub innehåller också knappen **Distribuera till Azure.** Om du klickar på den öppnas en anpassad distributionssida i Azure-portalen. På den här sidan kan du ange eller välja värden för var och en av parametrarna från de parametrar eller [valfria parametertabeller](#optional-parameters) som [krävs.](#required-parameters) När du har fyllt i inställningarna initieras malldistributionen genom att klicka på knappen **Köp.**
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Nästa steg

- Information om hur du hanterar Time Series Insights-resurser med HJÄLP av REST-API:er finns i [Time Series Insights Management](https://docs.microsoft.com/rest/api/time-series-insights-management/).
