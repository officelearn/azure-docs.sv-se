---
title: Skapa en ny Azure Application Insights-resurs | Microsoft-dokument
description: Konfigurera application insights-övervakning manuellt för ett nytt live-program.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 0c8b9ccaa70a2fd1bf46c6f4537f54d702ecc48f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537584"
---
# <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Azure Application Insights visar data om ditt program i en Microsoft *Azure-resurs*. Att skapa en ny resurs är därför en del av [att konfigurera Application Insights för att övervaka ett nytt program][start]. När du har skapat den nya resursen kan du hämta dess instrumenteringsnyckel och använda den för att konfigurera Application Insights SDK. Instrumenteringsnyckeln länkar telemetrin till resursen.

## <a name="sign-in-to-microsoft-azure"></a>Logga in på Microsoft Azure

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

Logga in på [Azure-portalen](https://portal.azure.com)och skapa en application insights-resurs:

![Klicka på +-tecknet i det övre vänstra hörnet. Välj utvecklarverktyg följt av Application Insights](./media/create-new-resource/new-app-insights.png)

   | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Unikt värde | Namn som identifierar appen du övervakar. |
   | **Resursgrupp**     | myResourceGroup      | Namn på den nya eller befintliga resursgruppen som ska vara värd för App Insights-data. |
   | **Location** | USA, östra | Välj en plats nära dig eller i närheten av den plats där appen finns. |

> [!NOTE]
> Du kan använda samma resursnamn i olika resursgrupper, men det kan vara fördelaktigt att använda ett globalt unikt namn. Detta kan vara användbart om du planerar att [utföra korsresursfrågor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query#identifying-an-application) eftersom det förenklar den syntax som krävs.

Ange lämpliga värden i de obligatoriska fälten och välj sedan **Granska + skapa**.

![Ange värden i obligatoriska fält och välj sedan "granska + skapa".](./media/create-new-resource/review-create.png)

När appen har skapats öppnas en ny ruta. I den här rutan visas prestanda- och användningsdata om ditt övervakade program. 

## <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln

Instrumenteringsnyckeln identifierar den resurs som du vill associera telemetridata med. Du måste kopiera instrumenteringsnyckeln och lägga till den i programmets kod.

![Klicka och kopiera instrumenteringsnyckeln](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>Installera SDK i appen

Installera SDK för programinsikter i appen. Det här steget beror mycket på vilken typ av program du har.

Använd instrumenteringsnyckeln för att konfigurera [SDK som du installerar i programmet][start].

SDK innehåller standardmoduler som skickar telemetri utan att du behöver skriva någon ytterligare kod. Om du vill spåra användaråtgärder eller diagnostisera problem mer i detalj [använder du API:et][api] för att skicka din egen telemetri.

## <a name="creating-a-resource-automatically"></a>Skapa en resurs automatiskt

### <a name="powershell"></a>PowerShell

Skapa en ny application insights-resurs

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Exempel

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultat

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Den fullständiga PowerShell-dokumentationen för den här cmdleten och hur du hämtar instrumenteringsnyckeln finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsights?view=azps-2.5.0).

### <a name="azure-cli-preview"></a>Azure CLI (förhandsversion)

För att komma åt förhandsversionen av Azure CLI-kommandon för förhandsgranskningen måste du först köra:

```azurecli
 az extension add -n application-insights
```

Om du inte kör `az extension add` kommandot visas ett felmeddelande som lyder:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Nu kan du köra följande för att skapa din Application Insights-resurs:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Exempel

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultat

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

För den fullständiga Azure CLI-dokumentationen för det här kommandot och för att lära dig hur du hämtar instrumenteringsnyckeln finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create).

## <a name="next-steps"></a>Nästa steg
* [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md)
* [Utforska mått](../../azure-monitor/platform/metrics-charts.md)
* [Skriv analysfrågor](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
