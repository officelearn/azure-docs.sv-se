---
title: Så här skapar du Azure Resource Manager-mall
description: Beskriver hur du redigerar en Azure Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551038"
---
# <a name="create-azure-resource-manager-template"></a>Skapa Azure Resource Manager-mall

Den här artikeln beskrivs processen och de beslut du fattar när du skapar en Azure Resource Manager-mall. Den innehåller en översikt över exempel och funktioner som kan hjälpa dig när du redigerar mallen. Artikeln förutsätter att du distribuerar resurser till en resursgrupp. Om du vill distribuera resurser till din Azure-prenumeration, som att skapa Azure-principer eller rollbaserad åtkomstkontroll tilldelningar, se [skapa resursgrupper och resurser för en Azure-prenumeration](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Välj JSON-redigerare

Resource Manager-mallen är en JSON-fil. Du behöver ett bra verktyg att arbeta med JSON-filen. Du har många alternativ, men om du inte redan har en redigerare som du vill installera [Visual Studio Code (VS Code)](https://code.visualstudio.com/). 

När du har installerat VS Code lägger du till den [tillägget Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). Det här tillägget ingår många funktioner som förenklar redigering av mallen.

![Mall för Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

Skärmbilden visar en Resource Manager-mall som öppnas i Visual Studio Code. 

Se en självstudie för att installera tillägget för Resource Manager tools och hur du använder VS Code [Snabbstart: Skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Förstå mall

Vi ska gå igenom delarna av mallen för att förstå hur mallen fungerar. Mallen kanske inte har varje avsnitt. Avsnitten som du vill fokusera på är:

* Den [parametrar](resource-group-authoring-templates.md#parameters) avsnitt som visar de värden som du kan ange under distributionen för att anpassa den infrastruktur som har distribuerats. 

* Den [variabler](resource-group-authoring-templates.md#variables) avsnitt som visar värden som används i mallen.

* Den [functions](resource-group-authoring-templates.md#functions) avsnittet, som visar anpassade malluttryck som används i mallen.

* Den [resurser](resource-group-authoring-templates.md#resources) avsnitt som visar Azure-resurser som distribueras till din prenumeration.

* Den [matar ut](resource-group-authoring-templates.md#outputs) avsnitt som visar de värden som returneras när distributionen är klar.

## <a name="look-for-similar-templates"></a>Leta efter liknande mallar

Du kan ofta hitta en befintlig mall som distribuerar en lösning som liknar vad du behöver. Den [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/) har hundratals mallar från community-deltagare.

![Snabbstart för mallagret](./media/how-to-create-template/template-quickstart-repo.png)

Sök igenom databasen för en mall som liknar vad du behöver. Det är okej om mallen inte gör det exakt det du behöver, du kan anpassa den.

När du har hittat en mall, Välj **Sök på Github**, och sedan kopiera den **azuredeploy.json** från databasen. I VS Code, skapar du en ny fil med namnet **azuredeploy.json** och Lägg till innehållet i mallfilen som du kopierade från Snabbstart-databasen.

## <a name="add-resources"></a>Lägg till resurser

Vill du förmodligen anpassa mallen för att kontrollera att den gör exakt det du söker. Först granska de resurser som har distribuerats. Du kan behöva lägga till, ta bort eller ändra resurser i mallen. Beskrivningar och syntax för resurserna som finns i [mallreferensen för Azure Resource Manager](/azure/templates/).

![Mallreferens](./media/how-to-create-template/template-reference.png)

Gör alla nödvändiga ändringar när du har granskat dessa egenskaper. Rekommendationer om hur du definierar resurser finns i [resurser - rekommenderade metoder för](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Lägg till eller ta bort parametrar

Du kan också behöva justera parametrarna för mallen. Du kan lägga till eller ta bort parametrar baserat på hur mycket anpassning som du vill aktivera under distributionen. Rekommendationer om hur du använder parametrar finns i [parametrar – rekommenderade metoder för](template-best-practices.md#parameters).

## <a name="add-tags"></a>Lägga till taggar

Du kan lägga till taggar till dina resurser och organisera dem logiskt efter kategorier och dela upp faktureringskostnader. Det är enkelt att lägga till taggar, du kan använda dem i JSON för resursen. Till exempel har följande lagringskontot två taggar:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Du kan också lägga till taggar dynamiskt från parametrar. Mer information finns i [taggar i mallen](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Granska Mallfunktioner

Du kanske ser uttryck i mallen som omges av hakparenteser, till exempel `"[some-expression]"`. Dessa uttryck använda Mallfunktioner att dynamiskt skapa värden under distributionen.

Exempelvis kan se du ofta ett uttryck som:

```json
"name": "[parameters('siteName')]"
```

Uttrycket hämtar värdet för en parameter. Värdet är tilldelad till egenskapen name.

Alternativt kan det uppstå ett mer komplext uttryck som använder flera funktioner som:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Uttrycket hämtar ett objekt med egenskaperna för ett lagringskonto.

För att förstå vad funktionerna gör genom att granska den [mallreferensen för funktionen](resource-group-template-functions.md) dokumentation.

## <a name="create-more-than-one-instance"></a>Skapa fler än en instans

Ibland vill du skapa fler än en instans av en resurs. Exempelvis kan behöva du flera lagringskonton. Snarare än Upprepa resursen via din mall, du kan använda den `copy` syntaxen för att ange fler än en instans.

I följande exempel skapas tre lagringskonton:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Du kan också ange antalet instanser dynamiskt från en parameter. Mer information finns i [distribuera fler än en instans av en resurs eller egenskapen i Azure Resource Manager-mallar](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Villkorsstyrd distribution av en resurs

Du behöva ibland ange under distributionen om en resurs i mallen har distribuerats. Exempelvis kan du flexibiliteten att distribuera en ny resurs eller använda en befintlig resurs. Den `condition` elementet ger dig möjlighet att aktivera eller inaktivera distributionen för en resurs. När uttryck i elementet villkoret är sant, är resursen distribuerad. Om värdet är false hoppas resursen under distributionen.

I följande exempel distribueras villkorligt ett storage-konto:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Mer information finns i den [elementet](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Granska beroenden

Vissa resurser i din mall måste distribueras innan andra resurser. Till exempel måste SQLServer finnas innan SQL-databasen har skapats. Resource Manager fastställer implicit distributionsordning för resurser när de [refererar till funktionen](resource-group-template-functions-resource.md#reference) används. Men i vissa fall kan du behöva explicit definiera beroenden med hjälp av den `dependsOn` element. Granska din mall för att se om några beroenden som behöver läggas till. Var noga med att lägga till onödiga beroenden inte som de kan sakta ned distribution eller skapa cirkelreferenser.

Följande bild visar beroendeordningen för olika App Service-resurser:

![Web app-beroenden](./media/how-to-create-template/web-dependencies.png)

I följande exempel visas en del av en mall som definierar beroenden.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Mer information finns på sidan om att [definiera ordningen för distribution av resurser i Azure Resource Manager-mallar](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Granska rekommenderade metoder

Innan du distribuerar mallen, granska [Metodtips för Azure Resource Manager-mall](template-best-practices.md) att se om det finns några rekommenderade metoder som du vill implementera i mallen.

Om du vill använda mallen i olika Azure-molnmiljöer finns i [utveckla Azure Resource Manager-mallar för molnet konsekvens](templates-cloud-consistency.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill distribuera en mall, [distribuera med Azure CLI](resource-group-template-deploy-cli.md) eller [distribuera med PowerShell](resource-group-template-deploy.md).
* En stegvis Snabbstart om hur du skapar en mall finns i [skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* En lista över tillgängliga funktioner i en mall finns i [Mallfunktioner](resource-group-template-functions.md).
