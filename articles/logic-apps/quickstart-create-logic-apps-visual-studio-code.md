---
title: Skapa och hantera automatiserade arbetsflöden med Visual Studio Code – Azure Logic Apps | Microsoft Docs
description: Snabbstart att skapa och hantera logikappar med JSON i Visual Studio Code (VS-kod)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229625"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Snabbstart: Skapa och hantera arbetsflöden i automatiserade logikappar – Visual Studio Code

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Visual Studio Code, som du kan skapa och hantera logikappar som hjälper dig att automatisera uppgifter, arbetsflöden och processer för att integrera appar, data, system och tjänster mellan organisationer och företag. Den här snabbstarten visar hur du kan skapa och redigera arbetsflöde för logikappsdefinitioner genom att arbeta med arbetsflödet definitionsschema i JavaScript Object Notation (JSON) via en kodbaserad upplevelse. Du kan också arbeta med befintliga logikappar som redan distribuerats till <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> i molnet. 

Även om du kan utföra samma uppgifter i den <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> och i Visual Studio kan du börja snabbare i Visual Studio Code när du redan är bekant med logic app-definitioner och vill arbeta direkt i koden. Du kan till exempel inaktivera, aktivera, ta bort och uppdatera redan skapat logikappar. Dessutom kan du arbeta i logic apps och integrationskonton från alla plattformar där Visual Studio Code körs, till exempel Linux, Windows och Mac.

I den här artikeln skapar du samma logikapp som i den [Snabbstart för att skapa en logikapp i Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md), som mer fokuserar på de grundläggande begreppen. I Visual Studio Code ser logikappen ut som i följande exempel:

![Färdig logikapp](./media/create-logic-apps-visual-studio-code/overview.png)

Innan du börjar bör du kontrollera att du har följande:

* Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Grundläggande kunskaper om [arbetsflöde för logikappsdefinitioner](../logic-apps/logic-apps-workflow-definition-language.md) och deras struktur som använder JavaScript Object Notation (JSON) 

  Om du inte har använt Logic Apps försök snabbstarten går du igenom [hur du skapar din första logikapp i Azure-portalen](../logic-apps/quickstart-create-first-logic-app-workflow.md), som mer fokuserar på de grundläggande begreppen. 

* Tillgång till Internet för att logga in till Azure och din Azure-prenumeration

* Hämta och installera följande verktyg, om du inte redan har dem: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code-version 1.25.1 eller senare</a>, som är kostnadsfritt

  * Visual Studio Code-tillägg för Azure Logic Apps

    Du kan hämta och installera tillägget från den [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) eller direkt från inuti Visual Studio Code. 
    Kontrollera att du ladda om Visual Studio Code när du har installerat. 

    ![Hitta ”Visual Studio Code-tillägg för Azure Logic Apps”](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Om du vill kontrollera att tillägget har installerats, Azure ikon i Visual Studio Code-verktygsfältet. 

    ![Installerade tillägg](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Mer information finns i <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">tillägget Marketplace</a>. Du kan också visa och skicka in bidrag till det här tillägget öppen källkod version genom att besöka den [Azure Logic Apps-tillägg för Visual Studio Code på GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Logga in på Azure

1. Öppna Visual Studio Code. Välj ikonen Azure Visual Studio Code-verktygsfältet. 

   ![Välj Azure-ikonen](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. I fönstret Azure under **Logikappar**väljer **logga in på Azure**. 

   ![Välj ”logga in på Azure”](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   Nu uppmanas du att logga in med hjälp av den angivna autentiseringskoden. 

1. Kopiera Autentiseringskod och välj sedan **Kopiera & Öppna**, som du öppnar ett nytt webbläsarfönster.

   ![Inloggningsuppmaning](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Ange autentiseringskoden för. Välj **Fortsätt**.

   ![Ange kod](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Välj ditt Azure-konto. När du har loggat in kan du Stäng webbläsaren och gå tillbaka till Visual Studio Code.

   Fönstret Azure visas rutan Logic Apps och Integrationskonton i Azure-prenumerationer i ditt konto. 

   ![Välj en prenumeration](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Om du inte ser prenumerationerna som du förväntar dig, bredvid **Logikappar** etikett, Välj **Välj prenumerationer** (filter-ikonen). Hitta och välj de prenumeration som du vill.

1. Om du vill visa alla befintliga logikappar eller integrationskonton i Azure-prenumerationen, expandera din prenumeration.

   ![Visa logikappar och integrationskonton](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Skapa en logikapp

1. Om du inte har loggat in på Azure-prenumerationen från inuti Visual Studio Code, följer du stegen i den här artikeln att [logga in nu](#sign-in-azure).

1. Välj snabbmenyn för din prenumeration **skapa**.

   ![Välj ”Skapa”](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. I listan som visar Azure-resursgrupper i din prenumeration, väljer en befintlig resursgrupp eller **skapa en ny resursgrupp**. 

   Det här exemplet skapar en ny resursgrupp:

   ![Skapa ny resursgrupp](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Ange ett namn för din Azure-resursgrupp och tryck sedan på RETUR.

   ![Namnet på din resursgrupp](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Välj platsen för datacenter för var du vill spara din logikapp metadata.

   ![Välj plats](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Ange ett namn för logikappen och tryck sedan på RETUR.

   ![Namnge logikappen](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Den nya logikappen visas nu i fönstret Azure under din Azure-prenumeration. Nu kan du börja skapa din logikapp arbetsflödesdefinitionen.

1. Från snabbmenyn för din logikapp, Välj **öppna i redigeringsprogram**. 

   ![Öppna logikappen i Redigeraren](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code öppnas ett arbetsflöde definitionsmall för logikapp (. filen logicapp.json) så att du kan börja skapa logikappens arbetsflöde.

   ![Ny logic app-arbetsflödesdefinition](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Börja bygga din logikapp arbetsflödesdefinitionen i filen logic app-arbetsflöde definition mall. Teknisk referens finns i den [Definitionsspråk för arbetsflödet schemat för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   Här är en exempel-logic-definition. Vanligtvis är JSON-element visas i alfabetisk ordning i varje avsnitt, men det här exemplet visar ungefär de här elementen i den ordning som den logikapp stegen visas i designern.

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

1. När du är klar sparar du filen logic app-definition. När du uppmanas att bekräfta att överföra sina logikapp-definitioner till din Azure-prenumeration väljer du Visual Studio Code **överför**.

   ![Ladda upp den nya logikappen](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   När Visual Studio Code publiceras logikappen till Azure, kan du hitta din app nu live och på Azure-portalen. 

   ![Logikapp som publicerats i Azure-portalen](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Redigera logic app

Du kan öppna appens definitionsfilen för arbetsflödet i Visual Studio Code för att fungera på en befintlig logikapp som redan har distribuerats i Azure.

1. Om du inte har loggat in på Azure-prenumerationen från inuti Visual Studio Code, följer du stegen i den här artikeln att [logga in nu](#sign-in-azure).

1. I fönstret Azure under **Logikappar**, expandera din Azure-prenumeration och välj den logikapp som du vill ha. 

1. Din logikapp-menyn och välj **öppna i redigeringsprogram**. Eller, välja Redigera-ikonen bredvid logikappens namn.

   ![Öppna Redigeraren för befintliga logic app](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code öppnas det. filen logicapp.json för din logikapp arbetsflödesdefinitionen.

   ![Öppnade arbetsflöde för logikappsdefinitionen](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Gör dina ändringar i logikappens definition.

1. När du är klar kan du spara ändringarna.

1. När Visual Studio Code om du vill uppdatera din definition för logikappen i Azure-prenumerationen, välja **överför**. 

   ![Överför dina ändringar](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forumet för Azure Logic Apps</a>.
* Om du vill skicka in eller rösta på förslag på funktioner besöker du <a href="https://aka.ms/logicapps-wish" target="_blank">webbplatsen för Logic Apps-användarfeedback</a>.

