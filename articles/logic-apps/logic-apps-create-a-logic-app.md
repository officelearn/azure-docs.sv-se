---
title: "Skapa ditt första arbetsflöde mellan molnappar och molntjänster – Azure Logic Apps | Microsoft-dokument"
description: "Automatisera affärsprocesser för systemintegrering och EAI-scenarion (Enterprise Application Integration) genom att skapa och köra arbetsflöden i Azure Logic Apps"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: workflow, cloud apps, cloud services, business processes, system integration, enterprise application integration, EAI
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: jehollan; estfan; LADocs
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6634f0ee88e68f2fcb09fd7534a88677e8efa029
ms.lasthandoff: 04/03/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>Skapa ditt första logikapparbetsflöde för att automatisera processer mellan molnappar och molntjänster

Utan att skriva kod kan du automatisera affärsprocesser enklare och snabbare när du skapar och kör arbetsflöden med [Azure Logic Apps](logic-apps-what-are-logic-apps.md). Den här första exemplet visar hur du skapar ett basarbetsflöde för logikappar som kontrollerar ett RSS-flöde för att se om det finns nytt innehåll på en webbplats. När nya objekt visas i webbplatsens flöde skickar logikappen e-post från ett Outlook- eller Gmail-konto.

Du behöver dessa objekt för att skapa och köra en logikapp:

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

  Din Azure-prenumeration används för fakturering av användning av logikappar. Lär dig hur [användningsmätning](../logic-apps/logic-apps-pricing.md) och [prissättning](https://azure.microsoft.com/pricing/details/logic-apps) fungerar för Azure Logic Apps.

Det här exemplet kräver också dessa objekt:

* Ett Outlook.com-, Office 365 Outlook- eller Gmail-konto

    > [!TIP]
    > Om du har ett personligt [Microsoft-konto](https://account.microsoft.com/account) har du ett Outlook.com-konto. Om du har en Azure-arbetskonto eller -skolkonto har du annars ett **Office 365 Outlook**-konto.

* En länk till RSS-flödet på en webbplats. I det här exemplet används RSS-flödet för [MSDN Channel 9-webbplatsen](https://channel9.msdn.com/):`https://s.ch9.ms/Feeds/RSS`

## <a name="add-a-trigger-that-starts-your-workflow"></a>Lägg till en utlösare som startar arbetsflödet

En [*utlösare*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) är en händelse som startar logikappens arbetsflöde och är det första objekt som krävs av din logikapp.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal").

2. Välj **Ny** > **Enterprise-integration** > **Logikapp** från den vänstra menyn på det sätt som visas här:

     ![Azure Portal, Ny, Enterprise-integration, Logikapp](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > Du kan också välja **Ny**, skriva `logic app` i sökrutan och sedan trycka på Retur. Välj **Logikapp** > **Skapa**.

3. Namnge logikappen och välj din Azure-prenumeration. Skapa eller välj nu en Azure-resursgrupp som hjälper dig att organisera och hantera relaterade Azure-resurser. Välj slutligen datacenterplatsen som ska fungera som värd för logikappen. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

     ![Information om logikappar](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > När du väljer **Fäst på instrumentpanelen** visas logikappen på Azure-instrumentpanelen efter distributionen och öppnas automatiskt. Om logikappen inte visas på instrumentpanelen går du till ikonen **Alla resurser** och väljer **Visa mer**, och sedan din logikapp. Klicka på **Fler tjänster** på den vänstra menyn. Välj **Logic Apps** under **Enterprise-integration** och välj sedan din logikapp.

4. När du öppnar logikappen för första gången visas Logic App Designer med mallar som du kan använda för att komma igång. Välj nu **Tom logikapp** så att du kan bygga din logikapp från början.

    Logic App Designer öppnas och visar tillgängliga tjänster och *utlösare* som du kan använda i din logikapp.

5. Gå till sökrutan och skriv `RSS`, och välj den här utlösaren: **RSS – När ett flödesobjekt publiceras** 

    ![RSS-utlösare](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. Ange länken till det RSS-flöde för webbplatsen som du vill spåra. 

     Du kan också ändra **Frekvens** och **Intervall**. 
     Inställningarna avgör hur ofta logikappen ska söka efter nya objekt och returnera alla objekt som hittas under det angivna tidsintervallet.

     I det här exemplet ska vi kontrollera varje dag om det finns nya objekt upplagda på webbplatsen MSDN Channel 9.

     ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. Spara ditt arbete nu. (Välj **Spara** i designerkommandofältet.)

   ![Spara din logikapp](media/logic-apps-create-a-logic-app/save-logic-app.png)

   När du sparar aktiveras logikappen, men den kontrollerar för närvarande endast om det finns nya objekt i det angivna RSS-flödet. 
   För att göra det här exemplet mer användbart lägger vi till en åtgärd som din logikapp utför när utlösaren har utlösts.

## <a name="add-an-action-that-responds-to-your-trigger"></a>Lägga till en åtgärd som svarar på utlösaren

En [*åtgärd*](./logic-apps-what-are-logic-apps.md#logic-app-concepts) är en aktivitet som utförs av logikapparbetsflödet. När du lägger till en utlösare i logikappen kan du lägga till en åtgärd för att utföra åtgärder med data som genereras av den utlösaren. I vårt exempel lägger vi nu till en åtgärd som skickar e-post när nya objekt visas i webbplatsens RSS-flöde.

1. I designverktyget går du till utlösaren och väljer **Nytt steg** > **Lägg till en åtgärd** enligt följande:

   ![Lägga till en åtgärd](media/logic-apps-create-a-logic-app/add-new-action.png)

   I designern visas [tillgängliga anslutningsappar](../connectors/apis-list.md) så att du kan välja en åtgärd som ska utföras när utlösaren utlöses.

2. Baserat på ditt e-postkonto följer du stegen för Outlook eller Gmail.

   * Om du vill skicka e-post från Outlook-kontot skriver du `outlook` i sökrutan. Under **Tjänster** väljer du **Outlook.com** för personliga Microsoft-konton eller välj **Office 365 Outlook** Azure-arbetskonton eller -skolkonton. 
   Gå till **Åtgärder** och välj **Skicka ett e-postmeddelande**.

       ![Välj Outlook-åtgärden Skicka ett e-postmeddelande](media/logic-apps-create-a-logic-app/actions.png)

   * Om du vill skicka e-post från ditt Gmail-konto skriver du `gmail` i sökrutan. 
   Gå till **Åtgärder** och välj **Skicka e-post**.

       ![Välj ”Gmail – Skicka e-post”](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. När du uppmanas att ange autentiseringsuppgifter kan du logga in med användarnamnet och lösenordet till ditt e-postkonto. 

4. Ange uppgifter för den här åtgärden, som e-postmåladress, och välj parametrarna för data som ska inkluderas i e-postmeddelandet, till exempel:

   ![Välj data som ska ingå i e-postmeddelandet](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    Så om du väljer Outlook kan logikappen exempelvis se ut så här:

    ![Slutförd logikapp](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    Spara ändringarna. (Välj **Spara** i designerkommandofältet.)

6. Du kan nu manuellt köra din logikapp för testning. Gå till designerkommandofältet och välj **Kör**. Annars kan du låta din logikapp kontrollera det angivna RSS-flödet enligt det schema som du har konfigurerat.

   Om logikappen hittar nya objekt skickar logikappen ett e-postmeddelande som innehåller dina valda data. 
   Om inga nya objekt hittas hoppar logikappen över den åtgärd som skickar e-post.

7. Välj **Översikt** på logikappsmenyn om du vill övervaka och kontrollera din logikapps körnings- och utlösningshistorik.

   ![Övervaka och visa logikappens körnings- och utlösningshistorik](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Om inte den information du förväntar dig visas kan du välja **Uppdatera** i kommandofältet.

   Läs mer om logikappens status eller körnings- och utlösningshistorik, eller diagnostisera logikappen i avsnittet [Troubleshoot your logic app (Felsöka logikappen)](logic-apps-diagnosing-failures.md).

      > [!NOTE]
      > Logikappen fortsätter att köra tills du stänger av den. Välj **Översikt** på logikappmenyn om du vill inaktivera din app tillfälligt. Välj **Inaktivera** i kommandofältet.

Gratulerar! Du har precis konfigurerat och kört din första baslogikapp. Du också lära dig hur enkelt du kan skapa arbetsflöden för att automatisera processer och integrera molnappar och molntjänster, och det krävs ingen kod.

## <a name="manage-your-logic-app"></a>Hantera din logikapp

För att hantera din app kan du utföra uppgifter som att kontrollera status, redigera, visa historik, inaktivera eller ta bort din logikapp.

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal").

2. Klicka på **Fler tjänster** på den vänstra menyn. Välj **Logikappar** under **Enterprise-integration**. Välj din logikapp. 

   Du kan hitta dessa uppgifter för hantering av logikappar i logikappsmenyn:

   |Aktivitet|Steg| 
   |:---|:---| 
   | Visa appens status, körningshistorik och allmän information| Välj **Översikt**.| 
   | Redigera din app | Välj **Logic App Designer**. | 
   | Visa arbetsflödet för JSON-definitionen för appen | Välj **Logikapp kodvy**. | 
   | Visa åtgärder som utförs i logikappen | Välj **Aktivitetslogg**. | 
   | Visa tidigare versioner för din logikapp | Välj **Versioner**. | 
   | Inaktivera din app tillfälligt | Välj **Översikt** och sedan **Inaktivera** i kommandofältet. | 
   | Ta bort din app | Välj **Översikt** och sedan **Ta bort** i kommandofältet. Ange logikappens namn och välj **Ta bort**. | 

## <a name="get-help"></a>Få hjälp

I [Azure Logic Apps-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) kan du ställa frågor, besvara frågor och se vad andra Azure Logic Apps-användare håller på med.

På [webbplatsen för Azure Logic Apps-användarfeedback](http://aka.ms/logicapps-wish) kan du hjälpa till med att förbättra Azure Logic Apps och anslutningsapparna genom att rösta på förslag eller komma med egna förslag på förbättringar.

## <a name="next-steps"></a>Nästa steg

*  [Lägga till villkor och kör arbetsflöden](../logic-apps/logic-apps-use-logic-app-features.md)
*     [Mallar för logikappar](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [Skapa logikappar från Azure Resource Manager-mallar](../logic-apps/logic-apps-arm-provision.md)

