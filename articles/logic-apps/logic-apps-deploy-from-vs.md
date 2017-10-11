---
title: Skapa, skapa och distribuera logikappar i Visual Studio - Azure Logic Apps | Microsoft Docs
description: "Skapa Visual Studio-projekt så att du kan utforma, skapa och distribuera Azure Logic Apps."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Utforma, skapa och distribuera Azure Logic Apps i Visual Studio

Även om den [Azure-portalen](https://portal.azure.com/) erbjuder ett bra sätt att skapa och hantera Azure Logic Apps kan du använda Visual Studio för att utforma, skapa och distribuera dina logic apps. Visual Studio innehåller omfattande verktyg som logik App Designer att skapa logikappar, konfigurera mallar för distribution och automatisering och distribuera till vilken miljö. 

Lär dig att komma igång med Azure Logikappar [hur du skapar din första logikapp i Azure portal](logic-apps-create-a-logic-app.md).

## <a name="installation-steps"></a>Installationssteg

Följ dessa steg för att installera och konfigurera Visual Studio tools för Logikappar i Azure.

### <a name="prerequisites"></a>Krav

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) eller Visual Studio 2015
* [Den senaste Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 eller senare)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Tillgång till Internet när du använder embedded designer

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Installera Visual Studio tools för Azure Logic Apps

När du har installerat krav:

1. Öppna Visual Studio. På den **verktyg** väljer du **tillägg och uppdateringar**.
2. Expandera den **Online** kategori så att du kan söka online.
3. Bläddra eller Sök efter **Logikappar** tills du hittar **Azure Logic Apps Tools för Visual Studio**.
4. Om du vill hämta och installera tillägget genom att klicka på **hämta**.
5. Starta om Visual Studio efter installationen.

> [!NOTE]
> Du kan också hämta [Azure Logic Apps Tools för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) och [Azure Logic Apps verktyg för Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) direkt från Visual Studio Marketplace.

När du har slutfört installationen kan du kan använda Azure-resursgrupp projektet med logik App Designer.

## <a name="create-your-project"></a>Skapa projektet

1. På den **filen** gå till menyn **ny**, och välj **projekt**. Eller Lägg till ditt projekt i en befintlig lösning, gå till **Lägg till**, och välj **nytt projekt**.

    ![Arkivmenyn](./media/logic-apps-deploy-from-vs/filemenu.png)

2. I den **nytt projekt** fönstret hitta **moln**, och välj **Azure-resursgrupp**. Namnge projektet och klicka på **OK**.

    ![Lägg till nytt projekt](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Välj den **Logikapp** mall som skapar en tom logik app Distributionsmall som du kan använda. När du har valt en mall klickar du på **OK**.

    ![Välj logiska appmallen](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Du har nu lagt till ditt logik app-projekt i lösningen. 
    I Solution Explorer ska distributionsfilen visas.

    ![Distribution av fil](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Skapa din logikapp med logik App Designer

När du har en Azure-resursgrupp projekt som innehåller en logikapp kan öppna du logik App Designer i Visual Studio för att skapa arbetsflödet. 

> [!NOTE]
> Designern kräver en Internetanslutning till frågan kopplingar för tillgängliga egenskaper och data. Om du använder Dynamics CRM Online-anslutningen frågar designern din CRM-instans om du vill visa tillgängliga anpassade och standardegenskaperna.

1. Högerklicka på din `<template>.json` fil och markera **öppna med logik App Designer**. (`Ctrl+L`)

2. Välj Azure-prenumeration, resursgrupp och plats för mallen för distribution.

    > [!NOTE]
    > Designa en logikapp skapar API-anslutningen resurser frågan för egenskaper under design. Visual Studio använder den valda resursgruppen för att skapa anslutningarna vid designtillfället. Om du vill visa eller ändra alla API-anslutningar, gå till Azure-portalen och bläddra efter **API anslutningar**.

    ![Väljaren för prenumeration](./media/logic-apps-deploy-from-vs/designer_picker.png)

    Designern använder definitionen i den `<template>.json` filen för återgivning.

4. Skapa och utforma din logikapp. Mallen för distribution har uppdaterats med dina ändringar.

    ![Logik App Designer i Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio lägger till `Microsoft.Web/connections` resurser till dina resursfilen för alla anslutningar logikappen behöver för att fungera. Dessa anslutningsegenskaper kan anges när du distribuerar och hanteras när du distribuerar i **API anslutningar** i Azure-portalen.

### <a name="switch-to-json-code-view"></a>Växla till kodvy JSON

Om du vill visa JSON-representation för din logikapp, Välj den **kodvy** fliken längst ned i designern.

Om du vill växla tillbaka till den fullständiga resursen JSON, högerklicka på den `<template>.json` fil och markera **öppna**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Lägg till referenser för beroende resurser till mallar för distribution av Visual Studio

När du vill att din logikapp för att referera till beroende resurser, kan du använda [Azure Resource Manager Mallfunktioner](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) i mallen för logik app-distribution. Exempelvis kanske du vill logikappen att referera till ett Azure-funktion eller integreringspaket konto som du vill distribuera tillsammans med din logikapp. Följ dessa riktlinjer om hur du använder parametrar i mallen för distribution så att logik App Designern återger korrekt. 

Du kan använda logik app parametrar i dessa typer av utlösare och åtgärder:

*   Underordnat arbetsflöde
*   Funktionsapp
*   APIM anrop
*   API-anslutning runtime-URL
*   Sökväg för API-anslutning

Och du kan använda Mallfunktioner, till exempel parametrar, variabler, resourceId, concat osv. Här är exempelvis hur du kan ersätta resurs-ID för Azure-funktion:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

Och där du ska använda parametrarna:

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
Ett annat exempel parameterstyra du Service Bus skicka meddelandet igen:

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
> För logik App Designer ska fungera när du använder parametrar måste du ange standardvärden, till exempel:
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

### <a name="save-your-logic-app"></a>Spara din logikapp

Om du vill spara din logikapp när som helst gå till **filen** > **spara**. (`Ctrl+S`) 

Om din logikapp har fel när du sparar din app, visas de i Visual Studio **utdata** fönster.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Distribuera din logikapp från Visual Studio

Du kan distribuera direkt från Visual Studio i ett par steg när du har konfigurerat din app. 

1. Högerklicka på projektet i Solution Explorer och gå till **distribuera** > **ny distribution...**

    ![Ny distribution](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. När du uppmanas logga in på Azure-prenumerationen. 

3. Nu måste du välja information för resursgruppen där du vill distribuera din logikapp. När du är klar väljer du **distribuera**.

    > [!NOTE]
    > Kontrollera att du väljer rätt mall och parametrar fil för resursgruppen. Om du vill distribuera till en produktionsmiljö kan du till exempel välja parameterfilen produktion.

    ![Distribuera till resursgrupp](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    Distributionens status visas i den **utdata** fönster. 
    Du kan behöva välja **Azure etablering** i den **visa utdata från** lista.

    ![Statusresultat för distribution](./media/logic-apps-deploy-from-vs/output.png)

I framtiden, kan du redigera din logikapp i källkontrollen och använda Visual Studio för att distribuera nya versioner.

> [!NOTE]
> Om du ändrar definitionen i Azure portal direkt, skrivs dessa ändringar över när du distribuerar från Visual Studio nästa gång. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Lägg till din logikapp till ett befintligt projekt i resursgruppen.

Om du har en befintlig resursgrupp-projekt kan du lägga till din logikapp projektet i fönstret JSON-disposition. Du kan också lägga till en annan logikapp tillsammans med den app som du skapade tidigare.

1. Öppna filen `<template>.json`.

2. Om du vill öppna fönstret JSON-disposition, gå till **visa** > **andra fönster** > **JSON-disposition**.

3. Klicka på Lägg till en resurs i mallen, **Lägg till resurs** längst upp i fönstret JSON-disposition. Eller högerklicka i fönstret JSON-disposition **resurser**, och välj **Lägg till ny resurs**.

    ![JSON-disposition](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. I den **Lägg till resurs** dialogrutan, lokaliserar och markerar **Logikapp**. Namnge din logikapp och välj **Lägg till**.

    ![Lägga till en resurs](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Nästa steg

* [Hantera logikappar med Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Visa vanliga exempel och scenarier](logic-apps-examples-and-scenarios.md)
* [Lär dig att automatisera affärsprocesser med Azure Logikappar](http://channel9.msdn.com/Events/Build/2016/T694)
* [Lär dig hur du integrerar dina system med Azure Logikappar](http://channel9.msdn.com/Events/Build/2016/P462)
