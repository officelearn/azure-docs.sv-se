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
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128608"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Skapa Azure Resource Manager-mallar för distribution av logikappar

När du skapar en logikapp, kan du expandera logikappens definition i en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-overview.md). Du kan sedan använda den här mallen för att automatisera distributioner genom att definiera resurserna och parametrar som du vill använda för distribution och ge parametervärden via en [parameterfilen](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
På så sätt kan du kan distribuera logic apps enkelt och i valfri miljö eller Azure-resursgrupp som du vill. 

Azure Logic Apps ger en [fördefinierade logic apps Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) att du kan återanvända, inte bara för att skapa logikappar, men också att definiera resurser och parametrar som ska användas för distributionen. Du kan använda den här mallen för dina egna affärsscenarier eller anpassa mallen så att den uppfyller dina krav. Läs mer om [Resource Manager-mall strukturen och syntaxen](../azure-resource-manager/resource-group-authoring-templates.md). JSON-syntax och egenskaper finns i [Microsoft.Logic resurstyper](/azure/templates/microsoft.logic/allversions).

Mer information om Azure Resource Manager-mallar finns i de här artiklarna:

* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)
* [Utveckla Azure Resource Manager-mallar för att få konsekvens i molnet](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Logic app-struktur

Din logikappsdefinitionen har dessa grundläggande avsnitt som du kan visa genom att växla från ”designverktygsvyn” till ”kodvy” eller genom att använda ett verktyg som [Azure Resource Explorer](http://resources.azure.com). Logikappsdefinitioner använda Javascript Object Notation (JSON), så Läs mer om JSON-syntax och egenskaper, se [Microsoft.Logic resurstyper](/azure/templates/microsoft.logic/allversions).

* **Logikappresursen**: Beskriver information, till exempel logikappens plats eller region, priser planerings- och arbetsflödesdefinitionen.

* **Arbetsflödesdefinitionen**: Beskriver logikappens utlösare och åtgärder och hur Azure Logic Apps-tjänsten körs arbetsflödet. I kodvyn kan du hitta arbetsflödesdefinitionen i den `definition` avsnittet.

* **Anslutningar**: Om du använder hanterade anslutningsappar i din logikapp den `$connections` avsnittet refererar till andra resurser som på ett säkert sätt lagra metadata om dessa anslutningar mellan din logikapp och andra system och tjänster, till exempel anslutningssträngar och åtkomst-token. I logic app-definition referenser till dessa anslutningar visas i din logikappsdefinitionen `parameters` avsnittet.

Om du vill skapa en mall för logikapp som du kan använda med distribution av Azure resursgrupper måste du definierar resurserna och Parameterisera efter behov. Till exempel om du distribuerar till en utveckling, testning och produktionsmiljö, vill du förmodligen använda olika anslutningssträngar till SQL database i varje miljö.
Eller så kanske du vill distribuera i olika prenumerationer eller resursgrupper.

## <a name="create-logic-app-deployment-templates"></a>Skapa logic app-distributionsmallar

Använd Visual Studio och Azure Logic Apps Tools för Visual Studio-tillägget för det enklaste sättet att skapa en giltig logic app-Distributionsmall. Genom att ladda ned din logikapp från Azure-portalen i Visual Studio, kan du få en giltig Distributionsmall som du kan använda med alla Azure-prenumeration och plats. Dessutom funktionsfråga ladda ned logikappen automatiskt logikappens definition som är inbäddad i mallen.
Mer information om att skapa och hantera logic apps i Visual Studio finns i [skapa logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) och [hantera logic apps i Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Än Visual Studio eller manuellt skapa mallen och de nödvändiga parametrarna genom att följa riktlinjerna i det här avsnittet, kan du också använda den [PowerShell-modulen för att skapa logic appmallar](https://github.com/jeffhollan/LogicAppTemplateCreator). Den här modulen med öppen källkod utvärderar först logikappen och alla anslutningar som använder logikappen. Modulen genererar sedan Mallresurser med de nödvändiga parametrarna för distributionen. Anta exempelvis att du har en logikapp som tar emot ett meddelande från en Azure Service Bus-kö och läggs data till en Azure SQL database. Verktyget modulen bevarar orchestration-logiken och funktionsfråga anslutningssträngar för SQL och Service Bus så att du kan ange dessa värden vid distributionen.

> [!IMPORTANT]
> Anslutningar måste finnas i samma Azure-resursgruppen som logikappen.
> För PowerShell-modul att arbeta med alla Azure-klient och prenumeration access token, Använd sedan modulen med den [Azure Resource Manager-klientverktyg](https://github.com/projectkudu/ARMClient). Mer information finns i den här [artikel om klientverktyget Azure Resource Manager-](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) beskriver ARMClient i mer detalj.

### <a name="install-powershell-module-for-logic-app-templates"></a>Installera PowerShell-modulen för mallar för logikappar

För det enklaste sättet att installera modulen från den [PowerShell-galleriet](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), Använd det här kommandot:

`Install-Module -Name LogicAppTemplate`

Du kan också manuellt installera PowerShell-modulen:

1. Hämta senaste [Logic App som skapar mallen](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Extrahera mappen i mappen PowerShell-modulen, som vanligtvis är `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Skapa mall för logikapp – PowerShell

När du har installerat PowerShell kan du generera en mall med hjälp av följande kommando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` är Azure prenumerations-ID. Den här raden först hämtar en token via ARMClient, och sedan rör vidarebefordras till PowerShell-skript och skapar sedan mallen i en JSON-fil.

## <a name="parameters-in-logic-app-templates"></a>Parametrarna i mallar för logikappar

När du har skapat din mall för logikapp kan du lägga till och redigera alla nödvändiga parametrar. Mallen har fler än en `parameters` avsnittet, till exempel: 

* Din logikapp arbetsflödesdefinitionen har sin egen [ `parameters` avsnittet](../logic-apps/logic-apps-workflow-definition-language.md#parameters) där du kan definiera alla parametrar som logikappen använder för att acceptera indata vid distributionen.

* Resource Manager-mallen har sin egen [ `parameters` avsnittet](../azure-resource-manager/resource-group-authoring-templates.md#parameters), separat från din logikapp `parameters` avsnittet. Exempel:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Anta exempelvis att din logikappsdefinitionen hänvisar till ett resurs-ID som representerar en Azure-funktion eller en kapslad logikappens arbetsflöde och du vill distribuera resurs-ID tillsammans med din logikapp som en distribution. Du kan lägga till det ID som en resurs i mallen och Parameterisera som ID. Du kan använda samma metod för referenser till anpassade API: er eller OpenAPI slutpunkter (tidigare ”Swagger”) som du vill distribuerade med varje Azure-resursgrupp.

När du använder parametrar i mallen för distribution, följ den här vägledningen så Logic Apps Designer kan visa de här parametrarna korrekt:

* Använda parametrar i dessa utlösare och åtgärder:

  * Azure Functions-app
  * Kapslade eller underordnade logikappens arbetsflöde
  * API Management-anrop
  * Körnings-URL för API-anslutning
  * Sökväg för API-anslutning

* När du definierar parametrar, se till att du anger standardvärden med hjälp av den `defaultValue` egenskapsvärde, till exempel:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Skydda dina parametrar för att skydda eller dölja känslig information i mallar. Läs mer om [hur du använder säker parametrar](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Här är ett exempel som visar hur du kan Parameterisera åtgärden ”Send message” Azure Service Bus:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Referens för beroende resurser

Om din logikapp kräver referenser till beroende resurser, kan du använda [Azure Resource Manager-Mallfunktioner](../azure-resource-manager/resource-group-template-functions.md) i din logikapp Distributionsmall. Anta exempelvis att du vill att din logikapp för att referera till en Azure-funktion eller ett integrationskonto med definitioner för partners, avtal och andra artefakter som du vill att distribueras tillsammans med din logikapp.
Du kan använda funktioner för Resource Manager-mall som `parameters`, `variables`, `resourceId`, `concat`och så vidare.

Här är ett exempel som visar hur du kan ersätta resurs-ID för en Azure-funktion genom att definiera dessa parametrar:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Här är hur du använder dessa parametrar när du refererar till Azure-funktion:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Lägg till logikappen i resursgruppsprojektet

Om du har ett befintligt Azure-resursgrupp-projekt kan du lägga till din logikapp i projektet med hjälp av JSON-disposition. Du kan också lägga till en annan logikapp tillsammans med appen som du skapade tidigare.

1. I Solution Explorer öppnar du den `<template>.json` filen.

2. Från den **visa** menyn och välj **andra Windows** > **JSON-disposition**.

3. Om du vill lägga till en resurs i mallfilen väljer **Lägg till resurs** överst i JSON-disposition. Eller högerklicka i JSON-disposition **resurser**, och välj **Lägg till ny resurs**.

   ![JSON-disposition](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. I den **Lägg till resurs** dialogrutan, lokaliserar och markerar **Logikapp**. Namnge logikappen och välj **Lägg till**.

   ![Lägg till resurs](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera mallar för logikappar](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
