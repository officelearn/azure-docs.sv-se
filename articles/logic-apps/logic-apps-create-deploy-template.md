---
title: Skapa mallar för distribution för Azure Logic Apps | Microsoft Docs
description: Skapa Azure Resource Manager-mallar för distribution av logikappar
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: ffa619351ca4a4bfd3a812775ee7ff6cd71ddea4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53089709"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Skapa Azure Resource Manager-mallar för distribution av logikappar

När du har skapat en logikapp kan du skapa det som en Azure Resource Manager-mall.
På så sätt kan du kan enkelt distribuera logikappen till valfri miljö eller resursgrupp där du kan behöva.
Mer information om Resource Manager-mallar finns i [skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md) och [distribuera resurser med hjälp av Azure Resource Manager-mallar](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Distributionsmall för Logic app

En logikapp har tre huvudkomponenter:

* **Logikappresursen**: innehåller information om saker som priser plan, plats och arbetsflödesdefinitionen.
* **Arbetsflödesdefinitionen**: Beskriver logikappens arbetsflödessteg och hur Logic Apps-motorn bör köra arbetsflödet.
Du kan visa den här definitionen i din logikapp **kodvy** fönster.
I logikappresursen, hittar du den här definitionen i den `definition` egenskapen.
* **Anslutningar**: refererar till separata resurser som på ett säkert sätt lagra metadata om alla connector-anslutningar, till exempel en anslutningssträng och en åtkomst-token.
I logic app-resursen i logikappen som refererar till dessa resurser i den `parameters` avsnittet.

Du kan visa dessa delar av befintliga logikappar med hjälp av ett verktyg som [Azure Resource Explorer](http://resources.azure.com).

För att göra en mall för en logikapp som ska användas med distribution av resursgrupper, måste du definiera resurserna och Parameterisera efter behov.
Till exempel om du distribuerar till en utveckling, testning och produktionsmiljö, vill du förmodligen använda olika anslutningssträngar till SQL database i varje miljö.
Eller så kanske du vill distribuera i olika prenumerationer eller resursgrupper.  

## <a name="create-a-logic-app-deployment-template"></a>Skapa en logic app-Distributionsmall

Det enklaste sättet att ha en giltig logic app-Distributionsmall är att använda den [Visual Studio Tools för Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Visual Studio-verktyg Generera en giltig Distributionsmall som kan användas i någon prenumeration eller plats.

Några andra verktyg kan hjälpa dig när du skapar en logic app-Distributionsmall.
Du kan skapa manuellt, det vill säga med hjälp av de resurser som redan nämnts här för att skapa parametrarna efter behov.
Ett annat alternativ är att använda en [logic app som skapar mallen](https://github.com/jeffhollan/LogicAppTemplateCreator) PowerShell-modulen. Den här modulen med öppen källkod utvärderar först logikappen och alla anslutningar som använder, och sedan genererar Mallresurser med de nödvändiga parametrarna för distributionen.
Till exempel om du har en logikapp som tar emot ett meddelande från en Azure Service Bus-kö och läggs data till en Azure SQL-databas, verktyget bevarar orchestration-logiken och funktionsfråga SQL och Service Bus-anslutningen strängar så att de kan ställas in på distribuera Textjustering.

> [!NOTE]
> Anslutningar måste finnas inom samma resursgrupp som logikappen.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Installera PowerShell-modulen logic app-mall
Det enklaste sättet att installera modulen är den [PowerShell-galleriet](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), med hjälp av kommandot `Install-Module -Name LogicAppTemplate`.  

Du kan också installera PowerShell-modulen manuellt:

1. Ladda ned den senaste versionen av den [logic app som skapar mallen](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extrahera mappen för din PowerShell-modulen (vanligtvis `%UserProfile%\Documents\WindowsPowerShell\Modules`).

För modulen att arbeta med valfri klient och prenumeration access token, rekommenderar vi att du använder den med den [ARMClient](https://github.com/projectkudu/ARMClient) kommandoradsverktyget.  Detta [blogginlägget](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) beskriver ARMClient i mer detalj.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Skapa en mall för logikapp med hjälp av PowerShell
När du har installerat PowerShell kan du generera en mall med hjälp av följande kommando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` är Azure prenumerations-ID. Den här raden först hämtar en token via ARMClient, och sedan rör vidarebefordras till PowerShell-skript och skapar sedan mallen i en JSON-fil.

## <a name="add-parameters-to-a-logic-app-template"></a>Lägga till parametrar till en mall för logikapp
Du kan fortsätta att lägga till eller ändra parametrarna som du kan behöva när du har skapat din mall för logikapp. Om din definition innehåller ett resurs-ID till en Azure-funktion eller inkapslat arbetsflöde som du planerar att distribuera i en enkel distribution, kan du lägga till fler resurser i mallen och Parameterisera ID: N efter behov. Samma gäller för alla referenser till anpassade API: er eller Swagger slutpunkter som du förväntar dig att distribuera med varje resursgrupp.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Lägg till referenser för beroende resurser till mallar för distribution av Visual Studio

Om du vill att logikappen ska hänvisa till beroende resurser kan du använda [Azure Resource Manager-Mallfunktioner](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) i mallen för logic app-distribution. Exempelvis kan du din logikapp för att referera till ett konto för Azure-funktion eller integreringspaket som du vill distribuera tillsammans med din logikapp. Följ dessa riktlinjer om hur du använder parametrar i mallen för distribution så att Logic App Designer återges korrekt. 

Du kan använda logic app-parametrar i dessa typer av utlösare och åtgärder:

*   Underordnat arbetsflöde
*   Funktionsapp
*   APIM-anrop
*   Körnings-URL för API-anslutning
*   Sökväg för API-anslutning

Och du kan använda Mallfunktioner, till exempel parametrar, variabler, resourceId, concat osv. Till exempel är här hur du kan ersätta Azure Function resurs-ID:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

Och där du ska använda parametrar:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Du kan Parameterisera meddelande för Service Bus Sändningsåtgärden för ett annat exempel:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl är valfritt och kan tas bort från din mall om det finns.
> 


> [!NOTE] 
> För Logic App Designer ska fungera när du använder parametrar, måste du ange standardvärden, till exempel:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Lägg till din logikapp i ett befintligt resursgrupp-projekt

Om du har en befintlig resursgrupp-projekt kan du lägga till din logikapp i projektet i JSON-disposition. Du kan också lägga till en annan logikapp tillsammans med appen som du skapade tidigare.

1. Öppna filen `<template>.json`.

2. Öppna JSON-disposition genom att gå till **visa** > **andra Windows** > **JSON-disposition**.

3. Lägg till en resurs i mallfilen genom att klicka på **Lägg till resurs** överst i JSON-disposition. Eller högerklicka i JSON-disposition **resurser**, och välj **Lägg till ny resurs**.

    ![JSON-disposition](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. I den **Lägg till resurs** dialogrutan, lokaliserar och markerar **Logikapp**. Namnge logikappen och välj **Lägg till**.

    ![Lägg till resurs](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Distribuera en mall för logikapp

Du kan distribuera mallen med hjälp av alla verktyg som PowerShell, REST API, [Azure DevOps Azure Pipelines](#team-services), och för malldistribution via Azure portal.
Dessutom för att lagra värdena för parametrarna, rekommenderar vi att du skapar en [parameterfilen](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Lär dig hur du [distribuera resurser med Azure Resource Manager-mallar och PowerShell](../azure-resource-manager/resource-group-template-deploy.md) eller [distribuera resurser med Azure Resource Manager-mallar och Azure-portalen](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Godkänna OAuth-anslutningar

Efter distributionen fungerar logikappen slutpunkt till slutpunkt med giltiga parametrar.
Du måste dock fortfarande godkänna OAuth-anslutningar för att skapa en giltig åtkomsttoken.
Öppna logikappen i Logic Apps Designer för att godkänna OAuth-anslutningar och godkänna dessa anslutningar. Eller för automatisk distribution kan du använda ett skript samtycker till att varje OAuth-anslutning.
Det finns ett exempelskript på GitHub under den [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projekt.

<a name="team-services"></a>
## <a name="azure-devops-azure-pipelines"></a>Azure DevOps Azure Pipelines

Ett vanligt scenario för att distribuera och hantera en miljö är att använda ett verktyg som Azure Pipelines i Azure DevOps med en logic app-Distributionsmall. Azure DevOps innehåller en [distribuera Azure-resursgrupp](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/DeployAzureResourceGroup) uppgift att du kan lägga till någon version eller frigöra pipeline. Du måste ha en [tjänstens huvudnamn](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) för auktorisering för att distribuera, och du kan generera versionspipelinen.

1. I Azure Pipelines, väljer **tom** så att du skapar en tom pipeline.

    ![Skapa tomma pipelinen][1]

2. Välj alla resurser som du behöver för att detta är förmodligen inklusive logic app-mallen som skapas manuellt eller som en del av skapandeprocessen.
3. Lägg till en **Azure Resursgruppsdistribution** uppgift.
4. Konfigurera med en [tjänstens huvudnamn](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/), och referera till filerna mall och parametrar i mallen.
5. Fortsätt att bygga ut steg i processen för versionen för alla andra miljö, automatiserad testning och godkännare efter behov.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
