---
title: Automatisera arbets flöden med Visual Studio Code-Azure Logic Apps
description: Skapa eller redigera logiska appar underliggande JSON-definitioner med Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
ms.openlocfilehash: a1387ca4d94410bafb1b1024345b5e307e215e6f
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679152"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-with-visual-studio-code"></a>Snabb start: skapa och hantera arbets flödes definitioner för Logic app med Visual Studio Code

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio Code, kan du skapa och hantera Logi Kap par som hjälper dig att automatisera uppgifter, arbets flöden och processer för att integrera appar, data, system och tjänster mellan organisationer och företag. Den här snabb starten visar hur du kan skapa och redigera de underliggande arbets flödes definitionerna, som använder JavaScript Object Notation (JSON) för Logic Apps genom en kod baserad upplevelse. Du kan också arbeta med befintliga Logic Apps som redan har distribuerats till Azure.

Även om du kan utföra samma uppgifter i [Azure Portal](https://portal.azure.com) och i Visual Studio, kan du komma igång snabbare i Visual Studio Code när du redan är bekant med Logic app-definitioner och vill arbeta direkt i kod. Du kan till exempel inaktivera, aktivera, ta bort och uppdatera redan skapade Logic Apps. Du kan också arbeta med Logic Apps och integrations konton från valfri utvecklings plattform där Visual Studio Code körs, till exempel Linux, Windows och Mac.

I den här artikeln kan du skapa samma Logic-app från den här [snabb](../logic-apps/quickstart-create-first-logic-app-workflow.md)starten, vilket fokuserar på de grundläggande begreppen. I Visual Studio Code ser den logiska appen ut som det här exemplet:

![Exempel på logik app Workflow-definition](./media/create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Innan du börjar ska du kontrol lera att du har följande objekt:

* Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Grundläggande kunskaper om [Logic app Workflow-definitioner](../logic-apps/logic-apps-workflow-definition-language.md) och deras struktur enligt beskrivningen med JSON

  Om du inte har använt Logic Apps kan du prova den här [snabb](../logic-apps/quickstart-create-first-logic-app-workflow.md)starten, vilket skapar dina första Logi Kap par i Azure Portal och fokuserar på de grundläggande begreppen.

* Åtkomst till webben för att logga in på Azure och din Azure-prenumeration

* Hämta och installera följande verktyg, om du inte redan har dem:

  * [Visual Studio Code version 1.25.1 eller senare](https://code.visualstudio.com/), som är kostnads fri

  * Visual Studio Code-tillägg för Azure Logic Apps

    Du kan hämta och installera det här tillägget från [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) eller direkt inifrån Visual Studio Code. Se till att du läser in Visual Studio Code igen efter installationen.

    ![Hitta Azure Logic Apps "Visual Studio Code Extension för"](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Kontrol lera att tillägget är korrekt installerat genom att välja den Azure-ikon som visas i ditt Visual Studio Code-verktygsfält.

    ![Tillägget bekräfta är korrekt installerat](./media/create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Mer information finns i [tillägg Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Om du vill bidra till den här tilläggets version av öppen källkod går du till [Azure Logic Apps tillägget för Visual Studio Code på GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Öppna Visual Studio Code. Välj Azure-ikonen i verktygsfältet Visual Studio Code.

   ![Välj Azure-ikon i Visual Studio Code-verktygsfältet](./media/create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. I Azure-fönstret, under **Logic Apps**, väljer **du logga in på Azure**.

   ![Välj "logga in på Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   Nu uppmanas du att logga in med den angivna autentiseringsmetoden.

1. Kopiera koden för autentisering och välj sedan **kopiera & öppna**, vilket öppnar ett nytt webbläsarfönster.

   ![Kopiera autentiserings kod som ska användas för Azure-inloggning](./media/create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

1. Ange din autentiseringsnyckel. När du uppmanas väljer du **Fortsätt**.

   ![Ange autentiseringsmetod för Azure-inloggning](./media/create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Välj ditt Azure-konto. När du har loggat in kan du stänga webbläsaren och återgå till Visual Studio Code.

   I Azure-fönstret visar fönstret Logic Apps och fönstret integrations konton nu Azure-prenumerationerna i ditt konto.

   ![Välj din Azure-prenumeration](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Om du inte ser de prenumerationer som du förväntar dig väljer du alternativet **Välj prenumerationer** (filter ikon) bredvid **Logic Apps** etikett. Sök efter och välj de prenumerationer som du vill använda.

1. Om du vill visa befintliga Logic Apps eller integrations konton i din Azure-prenumeration expanderar du din prenumeration.

   ![Visa Logic Apps och integrations konton](./media/create-logic-apps-visual-studio-code/view-existing-logic-apps-azure.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Skapa en logikapp

1. Om du inte har loggat in på Azure-prenumerationen från Visual Studio Code följer du stegen i den här artikeln för att [Logga in nu](#sign-in-azure).

1. Från din prenumerations snabb meny väljer du **skapa**.

   ![Välj Skapa från menyn prenumeration](./media/create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

1. I listan som visar Azures resurs grupper i din prenumeration väljer du en befintlig resurs grupp eller **skapar en ny resurs grupp**.

   I det här exemplet skapas en ny resurs grupp:

   ![Skapa din nya Azure-resurs grupp](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Ange ett namn för Azure-resurs gruppen och tryck sedan på RETUR.

   ![Ange ett namn för din Azure-resurs grupp](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Välj plats för data Center där du vill spara din Logic Apps metadata.

   ![Välj Azure-plats för att spara metadata för Logic app](./media/create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Ange ett namn för din Logic app och tryck sedan på RETUR.

   ![Ange namn på din Logic app](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Din nya Logic app visas nu i Azure-fönstret under din Azure-prenumeration. Nu kan du börja skapa din Logic Apps arbets flödes definition.

1. Från din Logic Apps snabb meny väljer du **Öppna i redigeraren**.

   ![Öppna Logic app i kodvyn](./media/create-logic-apps-visual-studio-code/open-new-logic-app-visual-studio-code.png)

   Visual Studio Code öppnar en Logic app-mall för arbets flödes definitioner (. logicapp. JSON-fil) så att du kan börja skapa din Logic app-arbetsflöde.

   ![Ny logik app Workflow-definition](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Börja skapa din Logic Apps arbets flödes definition i mallen Logic app Workflow definition.
Teknisk referens finns i [språk schema för arbets flödes definition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Här är en exempel på en logik definition. Normalt visas JSON-element i alfabetisk ordning i varje avsnitt. Det här exemplet visar dock dessa element ungefär i den ordning som Logic Apps-stegen visas i designern.

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
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. När du är klar sparar du din definitions fil för Logic app. När Visual Studio Code efterfrågar dig att bekräfta att din Logic app-definition överförs till din Azure-prenumeration väljer du **Ladda upp**.

   ![Ladda upp ny Logic app till din Azure-prenumeration](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   När Visual Studio Code publicerar din Logic app till Azure kan du hitta din app nu Live och köra i Azure Portal.

   ![Logisk app Publicerad i Azure Portal](./media/create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Redigera Logic app

Om du vill arbeta med en Logic-app som har publicerats i Azure kan du öppna logik appens definition med hjälp av Visual Studio Code.

1. Om du inte har loggat in på Azure-prenumerationen från Visual Studio Code följer du stegen i den här artikeln för att [Logga in nu](#sign-in-azure).

1. I Azure-fönstret, under **Logic Apps**, expanderar du din Azure-prenumeration och väljer den logiska app som du vill använda.

1. Från din Logic Apps-meny väljer du **Öppna i redigerings**programmet. Eller Välj redigerings ikonen bredvid namnet på din Logic Apps.

   ![Öppna redigerings programmet för befintlig Logic app](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code öppnar. logicapp. JSON-filen för din Logic Apps arbets flödes definition.

   ![Öppen Logic app Workflow-definition](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Gör dina ändringar i din Logic Apps-definition.

1. När du är klar kan du spara ändringarna.

1. När Visual Studio Code meddelar dig att du uppdaterar din Logic app-definition i din Azure-prenumeration väljer du **överför**.

   ![Ladda upp dina ändringar till Logic app-definition](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> * [Skapa Logic Apps med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)