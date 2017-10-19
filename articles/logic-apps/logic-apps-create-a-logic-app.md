---
title: "Skapa ditt första automatiserade arbetsflöde mellan system och molntjänster – Azure Logic Apps | Microsoft Docs"
description: "Automatisera affärsprocesser och arbetsflöden för systemintegrering och EAI-scenarion (Enterprise Application Integration) genom att skapa och köra logikappar"
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Skapa din första logikapp för automatisering av arbetsflöden och processer via Azure Portal

Utan att skriva kod du integrera system och tjänster genom att skapa och köra automatiska arbetsflöden med [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). För att visa hur enkelt du kan automatisera uppgifter med ett arbetsflöde skapar den här självstudien en enkel logikapp som kontrollerar nytt innehåll i ett RSS-flöde på en webbplats och skickar ett e-postmeddelande för varje nytt objekt i feeden. 

![Översikt – det första logikapp-exemplet](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en utlösare för att starta logikappen när ett RSS-flödesobjekt har publicerats.
> * Lägg till en åtgärd för att skicka e-postmeddelande med information om RSS-flödesobjektet.
> * Kör och testa din logikapp.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration kan du [börja med ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). Annars kan du [registrera dig för en prenumeration enligt principen Betala per användning](https://azure.microsoft.com/pricing/purchase-options/).

* Ett e-postkonto från [valfri e-postleverantör som stöds av Azure Logic Apps](../connectors/apis-list.md) för att skicka meddelanden. Du kan till exempel använda Office 365 Outlook, Outlook.com, Gmail eller någon annan leverantör som stöds. Den här självstudien använder Office 365 Outlook.

  > [!TIP]
  > Om du har ett personligt [Microsoft-konto](https://account.microsoft.com/account) har du ett Outlook.com-konto. Om du har ett Azure-arbetskonto eller -skolkonto har du annars ett Office 365 Outlook-konto.

* En länk till RSS-flödet på en webbplats. Det här exemplet använder [RSS-feed för de viktigaste berättelserna från webbplatsen CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss):`http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Skapa en tom logikapp 

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal").

2. Välj **Ny** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Skapa din logikapp med de inställningar som anges i tabellen.

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *din-Azure-prenumeration* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *din-Azure-resursgrupp* | Skapa eller välj en Azure-resursgrupp som hjälper dig att organisera och hantera relaterade Azure-resurser. | 
   | **Plats** | *din-Azure-region* | Välj datacenterregion för att distribuera logikappen. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen** och sedan **Skapa**.

   Du har nu skapat en Azure-resurs för din logikapp. 
   När Azure distribuerar din logikapp visar Logic Apps Designer mallar för vanliga mönster så kan du sätta igång snabbare.

   > [!NOTE] 
   > När du väljer **Fäst på instrumentpanelen** visas logikappen på Azure-instrumentpanelen efter distributionen och öppnas automatiskt i Logic Apps Designer. Om inte kan du hitta och öppna din logikapp manuellt.

5. Under **Mallar** väljer du **Tom logikapp** så att du kan bygga din logikapp från början.

   ![Välja mall för logikapp](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar dig nu tillgängliga [*anslutningsappar*](../connectors/apis-list.md) och deras [*utlösare*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), som du använder för att starta logikappens arbetsflöde.

   ![Utlösare för logikappar](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Lägg till en utlösare för att starta arbetsflödet

Varje logikapp måste börja med en [*utlösare*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Utlösaren utlöses när en viss händelse inträffar eller när nya data uppfyller de villkor som du har angett. Logic Apps-motorn skapar sedan en logikappinstans för att köra arbetsflödet. Varje gång utlösaren utlöses skapar motorn ytterligare en separat instans som kör arbetsflödet för logikappen.

1. I sökrutan skriver du "rss" som filter. Välj den här utlösaren: **RSS – När ett flödesobjekt publiceras** 

   ![Välj utlösaren:”RSS – När ett flödesobjekt publiceras”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Ange länken till det RSS-flöde för webbplatsen som du vill spåra, till exempel `http://rss.cnn.com/rss/cnn_topstories.rss`. Ange intervall och frekvens för upprepningen. I det här exemplet ska du ange att egenskaperna ska kontrollera flödet varje dag. 

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Spara ditt arbete nu. Välj **Spara** i designerverktygsfältet.
Om du vill minimera och dölja utlösarens information markerar du utlösarens namnlist.

   ![Spara din logikapp](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Logikappen är nu live men gör inget annat än att söka efter nya objekt i RSS-flödet tills du lägger till åtgärder i arbetsflödet. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Lägga till en åtgärd som svarar på utlösaren

Lägg nu till en [ *åtgärd*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), vilket är en åtgärd som logikapparbetsflödet utför. I det här exemplet lägger du till en åtgärd som skickar e-postmeddelande när ett nytt objekt visas i RSS-flödet.

1. I Logic Apps Designer går du till utlösaren och väljer **+Nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-create-a-logic-app/add-new-action.png)

   I designern visas [tillgängliga anslutningsappar](../connectors/apis-list.md) så att du kan välja en åtgärd som ska utföras när utlösaren utlöses.

   ![Välj från åtgärdslistan](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. I sökrutan anger du "send email" (skicka e-post) som filter. Baserat på din e-leverantör söker du och väljer matchande anslutningsapp. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel: 

   * För ett arbets- eller skolkonto i Azure väljer du anslutningsappen Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du anslutningsappen Outlook.com. 
   * För Gmail-konton väljer du Gmail-anslutningsappen. 

   Vi kommer att fortsätta med anslutingsappen Office 365 Outlook. 
   Om du använder en annan leverantör förblir stegen desamma, men användargränssnittet kan vara annorlunda. 

   ![Välj den här åtgärden: "Office 365 Outlook - Send an email" (Office 365 Outlook – Skicka ett e-postmeddelande)](./media/logic-apps-create-a-logic-app/actions.png)

3. När du uppmanas att ange autentiseringsuppgifter kan du logga in med användarnamnet och lösenordet till ditt e-postkonto. 

4. Ange informationen som angetts i tabellen och välj de fält som du vill ska ingå i e-postmeddelandet.

   | Till | Steg | 
   | -- | ----- | 
   | Välj fält som är tillgängliga för arbetsflödet. | Klicka i en redigeringsruta så att listan **Dynamiskt innehåll** öppnas, eller välj **Lägg till dynamiskt innehåll**. | 
   | Visa övriga tillgängliga fält. | I listan **Dynamiskt innehåll** väljer du **Visa mer** för varje avsnitt.  | 
   | Lägg till tomma rader i en redigeringsruta. | Tryck på Skift + Enter. | 
   | Stäng listan **Dynamiskt innehåll**. | Välj **Lägg till dynamiskt innehåll** igen. | 
   ||| 

   ![Välj data som ska ingå i e-postmeddelandet](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Till** | *mottagarens-e-postadress* | Ange mottagarens e-postadress. I testsyfte kan du använda din egen e-postadress. | 
   | **Ämne** | Ny CNN-post: **Flödesrubrik** | Ange innehållet för e-postmeddelandets ämne. <p>För den här självstudien anger du den föreslagna texten och väljer utlösarens **Flödesrubrik**-fält, som visar flödesobjektets rubrik. | 
   | **Brödtext** | Titel: **Flödesrubrik** <p>Publiceringsdatum: **Flödets primära länk** <p>Länk: **Flödets primära länk** | Ange innehållet för e-postmeddelandets ämne. <p>För den här självstudien anger du den föreslagna texten och väljer sedan följande utlösarfält: <p>- **Flödesrubrik**, som visar flödesobjektets rubrik igen </br>- **Flödet publicerat den**, vilket visar objektets publiceringsdatum och -tid </br>- **Primär flödeslänk**, som visar flödesobjektets URL-adress | 
   |||| 

   > [!NOTE] 
   > Om du väljer ett fält som lagrar en matris lägger designerprogrammet automatiskt till en "For each"-loop (För varje) omkring åtgärden som refererar till matrisen. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen.

5. När du är klar kan du spara ändringarna. Välj **Spara** i designerverktygsfältet.

   ![Slutförd logikapp](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Om du vill testa din logikapp nu kan du fortsätta till nästa avsnitt.

## <a name="4-run-and-test-your-workflow"></a>4. Kör och testa arbetsflödet

1. Om du vill köra logikappen manuellt för att testa väljer du **Kör** i Designer-verktygsfältet. Annars kan du låta din logikapp kontrollera det angivna RSS-flödet enligt det schema som du har konfigurerat.

   ![Kör logikappen](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Om logikappen hittar nya objekt skickar logikappen ett e-postmeddelande som innehåller dina valda data, till exempel:

   ![E-postmeddelande skickat för nytt RSS-flödesobjekt](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Om din logikapp inte hittar några nya objekt hoppar den över den åtgärd som skickar e-post och väntar på nästa intervall innan nästa kontroll. 

2. Välj **Översikt** på logikappsmenyn om du vill granska din logikapps körnings- och utlösningshistorik.
Om du vill visa mer information om en körning väljer du körningens rad.

   ![Övervaka och visa logikappens körnings- och utlösningshistorik](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Om inte den information du förväntar dig visas kan du välja **Uppdatera** i verktygsfältet.

   Oavsett om körningen gick att köra eller inte visar vyn Körningsinformation vilka steg som kördes eller inte kördes. 

   ![Visa information om en logikappkörning](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Läs mer om logikappens status, körnings- och utlösningshistorik, eller diagnostisera logikappen i avsnittet [Troubleshoot your logic app (Felsöka logikappen)](../logic-apps/logic-apps-diagnosing-failures.md).

3. Expandera det steg som du vill granska för att visa indata och utdata för varje steg. Den här informationen kan hjälpa dig att diagnostisera och felsöka problem i din logikapp. Exempel:

   ![Visa information om steg](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Mer information finns i [Övervaka din logikapp](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulerar! Du har nu konfigurerat och kört din första baslogikapp. Exemplet visar också hur enkelt du kan skapa arbetsflöden för att automatisera processer för att integrera system och tjänster – utan att skriva kod.

> [!NOTE]
> Logikappen fortsätter att köra tills du stänger av den. Om du vill stänga av appen tillfälligt fortsätter du till nästa avsnitt.

## <a name="clean-up-resources"></a>Rensa resurser

I den här självstudien används resurser och utförs åtgärder som kan innebära avgifter för din Azure-prenumeration. När du är klar med självstudien och testningen ska du se till att inaktivera eller ta bort resurser där du inte vill ha avgifter.

Du kan hindra logikappen från att köras och skicka e-post utan att ta bort appen. På logikappmenyn väljer du **Översikt**. I verktygsfältet väljer du **Inaktivera**.

![Stänga av din logikapp](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**F:** Vad kan jag mer göra med min logikapp? </br>
**S:** Det finns andra åtgärder som du kan utföra, till exempel redigera, visa JSON-definitionen, granska aktivitetsloggen eller ta bort din logikapp.

Om du vill hitta andra hanteringsuppgifter för logikappen kan du granska dessa kommandon på logikappmenyn:

| Aktivitet | Steg | 
| ---- | ----- | 
| Visa appens status, körnings- och utlösarhistorik och allmän information | Välj **Översikt**. | 
| Redigera din app | Välj **Logic App Designer**. | 
| Visa arbetsflödet för JSON-definitionen för appen | Välj **Logikapp kodvy**. | 
| Visa åtgärder som utförs i logikappen | Välj **Aktivitetslogg**. | 
| Visa tidigare versioner för din logikapp | Välj **Versioner**. | 
| Inaktivera din app tillfälligt | Välj **Översikt** och sedan **Inaktivera** i verktygsfältet. | 
| Ta bort din app | Välj **Översikt** och sedan **Ta bort** i verktygsfältet. Ange logikappens namn och välj **Ta bort**. | 
||| 

## <a name="get-support"></a>Få support

* Om du har frågor om Azure Logic Apps kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Vill du hjälpa till att förbättra Azure Logic Apps och anslutningsapparna? Rösta på eller skicka in förslag på [Azure Logic Apps User Voice-sidan](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* [Skapa din logikapp med Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Lägga till villkor och kör arbetsflöden](../logic-apps/logic-apps-use-logic-app-features.md)
*   [Mallar för logikappar](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Skapa logikappar från Azure Resource Manager-mallar](../logic-apps/logic-apps-arm-provision.md)
* [Logic Apps-användningsmätning](../logic-apps/logic-apps-pricing.md) 
* [Prissättning för Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps)
