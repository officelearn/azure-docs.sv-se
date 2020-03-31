---
title: Automatisera uppgifter med Visual Studio-kod
description: Skapa eller redigera logikapp underliggande JSON-definitioner med hjälp av Visual Studio Code (VS-kod)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/25/2019
ms.openlocfilehash: 819a60887743f39d3c2ffab3c955b2980cee2725
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74784841"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Snabbstart: Skapa och hantera logikapparbetsflödesdefinitioner med hjälp av Visual Studio-kod

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio Code kan du skapa och hantera logikappar som hjälper dig att automatisera uppgifter, arbetsflöden och processer för integrering av appar, data, system och tjänster i organisationer och företag. Den här snabbstarten visar hur du kan skapa och redigera underliggande arbetsflödesdefinitioner, som använder JSON (JavaScript Object Notation), för logikappar via en kodbaserad upplevelse. Du kan också arbeta med befintliga logikappar som redan har distribuerats till Azure.

Även om du kan utföra samma uppgifter i [Azure-portalen](https://portal.azure.com) och i Visual Studio kan du komma igång snabbare i Visual Studio-kod när du redan är bekant med logikappdefinitioner och vill arbeta direkt i kod. Du kan till exempel inaktivera, aktivera, ta bort och uppdatera redan skapade logikappar. Du kan också arbeta med logikappar och integrationskonton från alla utvecklingsplattformar där Visual Studio Code körs, till exempel Linux, Windows och Mac.

I den här artikeln kan du skapa samma logikapp från den här [snabbstarten](../logic-apps/quickstart-create-first-logic-app-workflow.md), som fokuserar mer på de grundläggande begreppen. I Visual Studio Code ser logikappen ut så här:

![Exempel på arbetsflödesdefinition för logikapp](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Innan du börjar bör du kontrollera att du har följande:

* Om du inte har ett Azure-konto och en prenumeration [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [logikapparbetsflödesdefinitioner](../logic-apps/logic-apps-workflow-definition-language.md) och deras struktur enligt beskrivningen med JSON

  Om du inte har tidigare i Logic Apps kan du prova den här [snabbstarten](../logic-apps/quickstart-create-first-logic-app-workflow.md), som skapar dina första logikappar i Azure-portalen och fokuserar mer på de grundläggande begreppen.

* Tillgång till webben för att logga in på Azure och din Azure-prenumeration

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio Code version 1.25.1 eller senare](https://code.visualstudio.com/), som är gratis

  * Tillägg för Visual Studio-kod för Azure Logic Apps

    Du kan hämta och installera det här tillägget från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) eller direkt inifrån Visual Studio Code. Se till att du laddar om Visual Studio-koden efter installationen.

    ![Hitta "Visual Studio-kodtillägg för Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Om du vill kontrollera att tillägget är korrekt installerat väljer du den Azure-ikon som visas i verktygsfältet Visual Studio-kod.

    ![Bekräfta att tillägget är korrekt installerat](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Mer information finns i [Till förlängningsmarknadsplats](https://code.visualstudio.com/docs/editor/extension-gallery). Om du vill bidra till tilläggets version med öppen källkod besöker du [tillägget Azure Logic Apps för Visual Studio Code på GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Öppna Visual Studio Code. Välj Azure-ikonen i verktygsfältet Visual Studio-kod.

   ![Välj Azure-ikon i verktygsfältet Visual Studio-kod](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Välj Logga in på Azure **under** **Logic Apps**i Azure-fönstret. När Microsofts inloggningssida får dig att logga in med ditt Azure-konto.

   ![Välj "Logga in på Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Om inloggningen tar längre tid än vanligt uppmanas du att logga in på en webbplats för Microsoft-autentisering genom att ange en enhetskod. Om du vill logga in med koden i stället väljer du **Använd enhetskod**.

      ![Fortsätt med enhetskoden i stället](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Om du vill kopiera koden väljer du **Kopiera & Öppna**.

      ![Kopiera kod för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Om du vill öppna ett nytt webbläsarfönster och fortsätta till autentiseringswebbplatsen väljer du **Öppna länk**.

      ![Bekräfta att öppna en webbläsare och gå till autentiseringswebbplats](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. På sidan **Logga in på ditt konto** anger du autentiseringskoden och väljer **Nästa**.

      ![Ange autentiseringskod för Azure-inloggning](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Välj ditt Azure-konto. När du har loggat in kan du stänga webbläsaren och återgå till Visual Studio-koden.

   I Azure-fönstret visar avsnitten **Logikappar** och **integrationskonton** nu de Azure-prenumerationer som är associerade med ditt konto. Om du inte ser de prenumerationer som du förväntar dig, eller om avsnitten visar för många prenumerationer, gör du så här:

   1. Flytta pekaren över logic **apps-etiketten.** När verktygsfältet visas väljer du **Välj prenumerationer** (filterikon).

      ![Söka efter eller filtrera Azure-prenumerationer](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Välj de prenumerationer som du vill ska visas i listan som visas.

1. Välj den prenumeration du vill använda under **Logic Apps.** Prenumerationsnoden expanderas och visar alla logikappar som finns i den prenumerationen.

   ![Välj din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Under **Integrationskonton**visar du alla integrationskonton som finns i prenumerationen om du väljer din prenumeration.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Skapa en ny logikapp

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration ännu inifrån Visual Studio-kod följer du [föregående steg för att logga in nu](#sign-in-azure).

1. Öppna prenumerationens snabbmeny under **Logic Apps**i Visual Studio-kod och välj **Skapa logikapp**.

   ![Välj "Skapa logikapp" på prenumerationsmenyn](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   En lista visas och visar alla Azure-resursgrupper i din prenumeration.

1. I resursgruppslistan väljer du antingen **Skapa en ny resursgrupp** eller en befintlig resursgrupp. Skapa i det här exemplet en ny resursgrupp.

   ![Skapa en ny Azure-resursgrupp](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Ange ett namn för din Azure-resursgrupp och tryck på RETUR.

   ![Ange namn för din Azure-resursgrupp](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Välj den Azure-region där du vill spara logikappens metadata.

   ![Välj Azure-plats för att spara logikappmetadata](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Ange ett namn på logikappen och tryck på Retur.

   ![Ange namn för logikappen](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   I Azure-fönstret visas din nya och tomma logikapp under din Azure-prenumeration. Visual Studio Code öppnar också en JSON-fil (.logicapp.json), som innehåller en arbetsflödesdefinition för skelettet för logikappen. Nu kan du börja manuellt redigera logikappens arbetsflödesdefinition i den här JSON-filen. Teknisk referens om strukturen och syntaxen för en arbetsflödesdefinition finns i [schemat för språkschema för arbetsflödesdefinition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Tom logikapp arbetsflödesdefinition JSON-fil](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Här följer till exempel en exempellösning för logikapparbetsflöde, som börjar med en RSS-utlösare och en Office 365 Outlook-åtgärd. Vanligtvis visas JSON-element i alfabetisk ordning i varje avsnitt. Det här exemplet visar dock dessa element ungefär i den ordning som logikappens steg visas i designern.

   > [!IMPORTANT]
   > Om du vill återanvända den här exempellogikens appdefinition behöver du till @fabrikam.comexempel ett Office 365-organisationskonto. Se till att du ersätter den fiktiva e-postadressen med din egen e-postadress. Om du vill använda en annan e-postkoppling, `Send_an_email_action` till exempel Outlook.com eller Gmail, ersätter du åtgärden med en liknande åtgärd som är tillgänglig från en [e-postanslutning som stöds av Azure Logic Apps](../connectors/apis-list.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. När du är klar sparar du logikappens arbetsflödesdefinition. (Arkiv-menyn > Spara eller tryck på Ctrl+S)

1. När du uppmanas att ladda upp logikappen till din Azure-prenumeration väljer du **Ladda upp**.

   Det här steget publicerar din logikapp till [Azure-portalen](https://portal.azure.com), vilket och gör din logik live och körs i Azure.

   ![Ladda upp ny logikapp till din Azure-prenumeration](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Visa logikapp i designer

I Visual Studio-kod kan du öppna logikappen i skrivskyddad designvy. Även om du inte kan redigera logikappen i designern kan du visuellt kontrollera logikappens arbetsflöde med hjälp av designervyn.

Öppna logikappens snabbmeny under **Logic Apps**i Azure-fönstret och välj Öppna **i Designer**.

Den skrivskyddade designern öppnas i ett separat fönster och visar logikappens arbetsflöde, till exempel:

![Visa logikapp i skrivskyddad designer](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Visa i Azure-portalen

Så här granskar du logikappen i Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com) med samma Azure-konto och prenumeration som är associerad med din logikapp.

1. Ange namnet på logikapparnas namn i Sökrutan på Azure-portalen. Välj logikapp i resultatlistan.

   ![Din nya logikapp i Azure portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Inaktivera eller aktivera logikappen

Om du redigerar en publicerad logikapp och sparar ändringarna i Visual Studio-kod skriver du *över* den redan distribuerade appen. Inaktivera logikappen först för att undvika att bryta logikappen i produktion och minimera störningar. Du kan sedan reaktiva logikappen när du har bekräftat att logikappen fortfarande fungerar.

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration ännu inifrån Visual Studio-kod följer du [föregående steg för att logga in nu](#sign-in-azure).

1. Expandera din Azure-prenumeration under **Logic Apps**i Azure-fönstret så att du kan visa alla logikappar i den prenumerationen.

   1. Om du vill inaktivera logikappen som du vill använda öppnar du logikappens meny och väljer **Inaktivera**.

      ![Inaktivera logikappen](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. När du är redo att återaktivera logikappen öppnar du logikappens meny och väljer **Aktivera**.

      ![Aktivera logikappen](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Redigera distribuerad logikapp

I Visual Studio-kod kan du öppna och redigera arbetsflödesdefinitionen för en redan distribuerad logikapp i Azure.

> [!IMPORTANT] 
> Innan du redigerar en logikapp som körs aktivt i produktion bör du undvika risken för att bryta logikappen och minimera störningar genom att [inaktivera logikappen först](#disable-enable-logic-app).

1. Om du inte har loggat in på ditt Azure-konto och din prenumeration ännu inifrån Visual Studio-kod följer du [föregående steg för att logga in nu](#sign-in-azure).

1. Expandera din Azure-prenumeration under **Logic Apps**i Azure-fönstret och välj den logikapp du vill använda.

1. Öppna logikappens meny och välj **Öppna i Redigerare**. Du kan också välja redigeringsikonen bredvid logikappens namn.

   ![Öppna redigeraren för befintlig logikapp](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio-kod öppnar filen .logicapp.json i den lokala tillfälliga mappen så att du kan visa logikappens arbetsflödesdefinition.

   ![Visa arbetsflödesdefinition för publicerad logikapp](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Gör dina ändringar i logikappens arbetsflödesdefinition.

1. När du är klar kan du spara ändringarna. (Arkiv-menyn > Spara eller tryck på Ctrl+S)

1. När du uppmanas att ladda upp dina ändringar och *skriva över* din befintliga logikapp i Azure-portalen väljer du **Ladda upp**.

   Det här steget publicerar dina uppdateringar till logikappen i [Azure-portalen](https://portal.azure.com).

   ![Ladda upp redigeringar till logikappdefinition i Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Visa eller befordra andra versioner

I Visual Studio-kod kan du öppna och granska tidigare versioner för logikappen. Du kan också befordra en tidigare version till den aktuella versionen.

> [!IMPORTANT] 
> Innan du ändrar en logikapp som körs aktivt i produktion bör du undvika risken för att bryta logikappen och minimera störningar genom att [inaktivera logikappen först](#disable-enable-logic-app).

1. Expandera din Azure-prenumeration under **Logic Apps**i Azure-fönstret så att du kan visa alla logikappar i den prenumerationen.

1. Expandera logikappen under din prenumeration och expandera **versioner**.

   I listan **Versioner** visas logikappens tidigare versioner, om sådana finns.

   ![Logikappens tidigare versioner](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Om du vill visa en tidigare version väljer du något avstegen:

   * Om du vill visa JSON-definitionen väljer du versionsnumret för den definitionen under **Versioner.** Du kan också öppna den versionens snabbmeny och välja **Öppna i Redigerare**.

     En ny fil öppnas på den lokala datorn och visar den versionens JSON-definition.

   * Om du vill visa versionen i den skrivskyddade designervyn öppnar du den versionens snabbmeny och väljer **Öppna i Designer**.

1. Så här befordrar du en tidigare version till den aktuella versionen:

   1. Öppna den tidigare versionens snabbmeny under **Versioner**och välj **Befordra**.

      ![Främja tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Om du vill fortsätta efter att Visual Studio Code uppmanar dig att bekräfta väljer du **Ja**.

      ![Bekräfta att befordra tidigare version](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code befordrar den valda versionen till den aktuella versionen och tilldelar ett nytt nummer till den befordrade versionen. Den tidigare aktuella versionen visas nu under den marknadsförda versionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa logikappar med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)