---
title: "Automatiserade arbetsflöden mellan system och molntjänster – Azure Logic Apps | Microsoft Docs"
description: "Skapa logikappar för att automatisera arbetsflöden för systemintegrering och EAI-scenarion (Enterprise Application Integration)"
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>Automatisera ditt första arbetsflöde för att bearbeta data med en logikapp

Om du vill integrera system och tjänster snabbare för din organisation kan du automatisera arbetsflöden och affärsprocesser med [Azure Logic Apps](../logic-apps/logic-apps-what-are-logic-apps.md). Snabbstarten introducerar hur enkelt du kan skapa och köra ett automatiserat arbetsflöde genom att skapa en logikapp. Exempelappen visar hur du kan automatisera ett arbetsflöde som genomsöker en webbplats RSS-flöde efter nya objekt och skickar ett e-postmeddelande för varje objekt.

Den här exempellogikappen skickar ett e-postmeddelande som i följande exempel:

![E-postmeddelande skickat för nytt RSS-flödesobjekt](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Och här är logikapparbetsflödet på hög nivå som du skapar:

![Översikt – exempel på logikapp](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

I den här snabbstarten lär du dig att:

> [!div class="checklist"]
> * Skapa en tom logikapp.
> * Lägg till en utlösare för att starta arbetsflödet när ett nytt objekt visar sig i RSS-flödet.
> * Lägg till en åtgärd för att skicka e-postmeddelande med information om RSS-flödesobjektet.
> * Kör logikappens arbetsflöde.

Om du inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

* Ett e-postkonto från valfri e-postleverantör som stöds av Azure Logic Apps för att skicka meddelanden. Du kan till exempel använda Office 365 Outlook, Outlook.com eller Gmail. Om du vill läsa om övriga e-postanslutningsprogram som stöds kan du [läsa listan över anslutningsappar](https://docs.microsoft.com/connectors/). I den här snabbstarten används Office 365 Outlook.

  > [!TIP]
  > Om du har ett personligt [Microsoft-konto](https://account.microsoft.com/account) har du ett Outlook.com-konto. Om du har ett Azure-arbetskonto eller -skolkonto har du annars ett Office 365 Outlook-konto.

* En länk till RSS-flödet på en webbplats. I det här exemplet används [RSS-feed för de viktigaste berättelserna från webbplatsen Reuters](http://feeds.reuters.com/reuters/topNews):`http://feeds.reuters.com/reuters/topNews`

Snabbstarten kräver inte att du skriver kod, men Logic Apps stöder övriga scenarier som använder kod, till exempel när du kör din egen kod från en logikapp med [Azure Functions](../azure-functions/functions-overview.md).

## <a name="create-a-blank-logic-app"></a>Skapa en tom logikapp 

1. Logga in på [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. Välj **Ny** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Skapa din logikapp med inställningarna i tabellen under den här bilden:

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Inställning | Föreslaget värde | Beskrivning | 
   | ------- | --------------- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *namn-på-Azure-prenumerationen* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *namn-på-Azure-resursgruppen* | Skapa en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) för den här logikappen och organisera alla resurser som är associerade med den här appen. | 
   | **Plats** | *din-Azure-datacenter-region* | Välj datacenterregion för att distribuera logikappen, till exempel USA, västra. | 
   | **Log Analytics** | Av | Aktivera diagnostikloggning för din logikapp. För den här snabbstarten ska du behålla inställningen **Av**. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen**. På så sätt visas logikappen automatiskt på din Azure-instrumentpanel och öppnas efter distributionen. Välj **Skapa**.

   > [!NOTE]
   > Om du inte vill fästa din logikapp måste du manuellt hitta och öppna logikappen så att du kan fortsätta.

   När Azure har distribuerat din logikapp öppnas Logic Apps Designer och visar en sida med en introduktionsvideo. 
   Under videon finns mallar för vanliga logikappmönster. 
   Den här snabbstarten skapar din logikapp från början. 

5. Rulla förbi introduktionsvideon och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   ![Välja tom mall för logikapp](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   Logic Apps Designer visar dig tillgängliga anslutningsappar och deras utlösare, som du använder för att starta logikappens arbetsflöde.

   ![Utlösare för logikappar](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>Lägg till en utlösare för att identifiera nya objekt

Varje logikapp börjar med en [utlösare](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). Utlösaren utlöses när en viss händelse inträffar eller när nya data uppfyller de villkor som du har angett. Varje gång utlösaren körs skapar Logic Apps-motorn en logikappinstans som startar och kör ditt arbetsflöde.

1. I sökrutan skriver du "rss" som filter. Välj den här utlösaren: **RSS – När ett flödesobjekt publiceras** 

   ![Välj utlösaren:”RSS – När ett flödesobjekt publiceras”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Ange länken till det RSS-flöde som du vill övervaka, till exempel `http://feeds.reuters.com/reuters/topNews`. Ange intervall och frekvens för upprepningen. Det här exemplet kontrollerar flödet var femte minut.

   ![Konfigurera utlösare med RSS-flöde, frekvens och intervall](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   Logic Apps skapar en anslutning till RSS-flödet.

   > [!TIP]
   > För att förenkla vyn i designerverktyget kan du komprimera och dölja formens detaljer – klicka bara inuti formens namnlist.

3. Spara ditt arbete. Välj **Spara** i designerverktygsfältet. 

   ![Spara din logikapp](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Logikappen har nu lanserats men gör inget annat än att kontrollera RSS-flödet. Därför ska vi lägga till en åtgärd som svarar när utlösaren utlöses.

## <a name="add-an-action-to-send-email"></a>Lägga till en åtgärd för att skicka e-post

Nu när du har en utlösare lägger du till en [åtgärd](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) som skickar e-postmeddelande när ett nytt objekt visas i RSS-flödet. Arbetsflödet utför denna åtgärd när utlösaren aktiveras.

1. I Logic Apps Designer går du till utlösaren och väljer **+Nytt steg** > **Lägg till en åtgärd**.

   ![Lägga till en åtgärd](./media/logic-apps-create-a-logic-app/add-new-action.png)

   Designverktyget visar åtgärderna som din logikapp kan vidta när utlösaren aktiveras.

   ![Välj från åtgärdslistan](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. I sökrutan anger du "send email" (skicka e-post) som filter. Sök rätt på och markera e-postanslutningsappen du vill använda. Välj sedan åtgärden "send email" (skicka e-post) för anslutningsappen. Exempel: 

   * För Azure arbets- eller skolkonto väljer du Office 365 Outlook. 
   * För personliga Microsoft-konton väljer du Outlook.com. 
   * Välj Gmail för Gmail-konton. 

   I den här snabbstarten används Office 365 Outlook. 
   Om du använder en annan e-post-leverantör förblir stegen desamma, men användargränssnittet kan vara annorlunda. 

   ![Välj den här åtgärden: "Office 365 Outlook - Send an email" (Office 365 Outlook – Skicka ett e-postmeddelande)](./media/logic-apps-create-a-logic-app/actions.png)

3. När du uppmanas att ange autentiseringsuppgifter kan du logga in med användarnamnet och lösenordet till ditt e-postkonto. 

   Logic Apps skapar en anslutning till ditt e-postkonto.

4. Ange nu de data du vill ska ingå i e-postmeddelandet. 

   1. Ange mottagarens e-postadress i fältet **Till**. 
   I testsyfte kan du använda din egen e-postadress.

   2. I rutan **Ämne** anger du ämne. 
   Ange till exempel "Nytt RSS-objekt: " enligt:

      ![Ange ämne för e-post](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      När du klickar inuti rutan öppnas **Lägg till dynamisk innehållslista** så att du kan välja tillgängliga datafält som ska ingå i åtgärden. 
      Om den dynamiska innehållslistan inte öppnas väljer du **Lägg till dynamiskt innehåll** under respektive redigeringsruta.

   3. Från listan **Lägg till dynamiskt innehåll** väljer du **Flödesrubrik**, vilket inkluderar objektets rubrik i e-postmeddelandet.

      ![Ange ämne för e-post](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      När du är klar ser ämnet ut som i det här exemplet:

      ![Tillagd flödesrubrik](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > Om du råkar välja ett fält som innehåller en matris, som **categories-item**, lägger designerprogrammet automatiskt till en "For each"-loop (För varje) omkring åtgärden som refererar till fältet. På så sätt kan din logikappsåtgärd utförs på varje element i matrisen. 
      > 
      > Om du vill ta bort loopen väljer du ellipserna (**...**) på loopens namnlist och väljer **Ta bort**.

   4. Ange innehållet för e-postmeddelandets ämne i rutan **Brödtext**. 
   För det här exemplet anger du texten och väljer dessa fält:

      ![Lägg till innehåll för e-postmeddelandets brödtext](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | Fält | Beskrivning | 
      | ----- | ----------- | 
      | **Flödesrubrik** | Visa objektets rubrik. | 
      | **Flödet publicerat den** | Visa objektets publiceringsdatum och -tid. | 
      | **Flödets primära länk** | Visa webbadressen för objektet. | 
      ||| 

      > [!TIP]
      > Tryck på Skift + Enter för att lägga till tomma rader i en redigeringsruta. 
      
5. Spara ditt arbete. Välj **Spara** i designerverktygsfältet.

   ![Slutförd logikapp](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>Kör logikappens arbetsflöde

Om du vill starta logikappen manuellt väljer du **Kör** i Designer-verktygsfältet. Du kan också vänta på att logikappen ska köras enligt schemat du har skapat.

![Kör logikappen](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

Om RSS-flödet innehåller nya objekt skickar din logikapp ett e-postmeddelande för varje nytt objekt. Här är ett exempel på ett e-postmeddelade i Outlook som den här logikappen skickar:

![E-postmeddelande skickat för nytt RSS-flödesobjekt](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

Om flödet inte innehåller några nya objekt hoppar den över steget som skickar e-post och väntar på nästa intervall innan nästa kontroll. 

> [!TIP]
> Om du inte får e-post kan du titta i mappen Skräppost. Om du är osäker på om din logikapp kördes korrekt kan du läsa informationen om att [felsöka logikappen](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulerar! Du har nu konfigurerat och kört din första logikapp. Den här snabbstarten har visat hur enkelt och snabbt du kan skapa automatiserade arbetsflöden för att integrera system och tjänster.

## <a name="clean-up-resources"></a>Rensa resurser

Din logikapp fortsätter att köras och eventuellt medför det kostnader på Azure-prenumerationen tills du inaktiverar eller tar bort din app. När du skapar anslutningar för logikappen finns anslutningarna också kvar, även när du har tagit bort logikappen. 

När du är klar ska du se till att inaktivera eller ta bort resurser där du inte vill ha avgifter eller resurser du inte vill behålla. Om du vill ta bort alla resurser som du skapade för den här snabbstarten tar du bort alla Azure-resursgrupper som du skapade för den här logikappen. 

### <a name="delete-resource-group"></a>Ta bort resursgrupp

Och om du inte vill behålla något som är kopplat till din logikapp tar du bort resursgruppen du skapade för den här snabbstarten och alla relaterade resurser. Lär dig mer om att [hantera Azure-resursgrupper](../azure-resource-manager/resource-group-portal.md#manage-resources).

1. På Azure-menyn väljer du **Resursgrupper**.

2. Välj den resursgrupp som du vill ta bort. På resursgruppmenyn väljer du **Översikt** om det inte redan är markerat. 

3. Granska alla resurser i gruppen du vill ta bort. När du är klar väljer du **Ta bort resursgrupp** på resursgruppens verktygsfält.

### <a name="turn-off-logic-app"></a>Stänga av logikappen

Om du vill sluta köra din logikapp utan att ta bort ditt arbete inaktiverar du appen. 

På logikappmenyn väljer du **Översikt**. I verktygsfältet väljer du **Inaktivera**.

  ![Stänga av din logikapp](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

### <a name="delete-logic-app"></a>Ta bort logikapp

Du kan ta bort enbart din logikapp, men behålla alla andra relaterade resurser, som anslutningarna du har skapat.

1. På logikappmenyn väljer du **Översikt**. I verktygsfältet väljer du **Ta bort**. 

   ![Ta bort din logikapp](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > Om du inte ser logikappmenyn kan du försöka återgå till Azure-instrumentpanelen och öppna logikappen på nytt.

2. Bekräfta att du vill ta bort din logikapp och välj **Ta bort**.

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa logikappsarbetsflöden från fördefinierade mallar](../logic-apps/logic-apps-create-logic-apps-from-templates.md)