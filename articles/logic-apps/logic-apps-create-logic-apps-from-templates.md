---
title: Skapa arbetsflöden för logikapp snabbare med hjälp av fördefinierade mallar
description: Skapa snabbt arbetsflöden för logikappar med hjälp av färdiga mallar som tillhandahålls av Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: aac0060527af7b7d880f971e7608be3fa44a2d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905109"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Skapa logikappsarbetsflöden från fördefinierade mallar

För att komma igång med att skapa arbetsflöden snabbare tillhandahåller Logic Apps mallar som är färdiga logikappar som följer vanliga mönster. Använd dessa mallar enligt dem eller redigera dem för att passa ditt scenario.

Här är några mallkategorier:

| Malltyp | Beskrivning | 
| ------------- | ----------- | 
| Molnmallar för företag | För att integrera Azure Blob, Dynamics CRM, Salesforce, Box och innehåller andra kopplingar för företagets molnbehov. Du kan till exempel använda dessa mallar för att ordna affärsleads eller säkerhetskopiera företagets fildata. | 
| Mallar för personlig produktivitet | Förbättra den personliga produktiviteten genom att ställa in dagliga påminnelser, omvandla viktiga arbetsobjekt till att göra-listor och automatisera långa uppgifter ner till ett enda användargodkännandesteg. | 
| Molnmallar för konsumenter | För att integrera sociala medietjänster som Twitter, Slack och e-post. Användbart för att stärka sociala medier marknadsföring initiativ. Dessa mallar innehåller också uppgifter som molnkopiering, vilket ökar produktiviteten genom att spara tid på traditionellt repetitiva uppgifter. | 
| Mallar för företagsintegreringspaket | För att konfigurera VETER-pipelines (validera, extrahera, omvandla, berika, dirigera), ta emot ett X12 EDI-dokument via AS2 och omvandla till XML och hantera X12-, EDIFACT- och AS2-meddelanden. | 
| Mallar för protokollmönster | För implementering av protokollmönster som begäran-svar över HTTP och integrationer över FTP och SFTP. Använd dessa mallar enligt de angivna mallarna eller bygg vidare på dem för komplexa protokollmönster. | 
||| 

Om du inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. Mer information om hur du skapar en logikapp finns i [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Skapa Logic Apps från mallar

1. Om du inte redan har gjort det loggar du in på [Azure-portalen](https://portal.azure.com "Azure Portal").

2. På Azure-huvudmenyn väljer du **Skapa en resurs** > **Enterprise Integration** > **Logic App**.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Skapa din logikapp med inställningarna i tabellen under den här bilden:

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *namn-på-Azure-prenumerationen* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *namn-på-Azure-resursgruppen* | Skapa eller välj en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) för den här logikappen och ordna alla resurser som är associerade med den här appen. | 
   | **Location** | *din-Azure-datacenter-region* | Välj datacenterregion för att distribuera logikappen, till exempel USA, västra. | 
   | **Logga Analytics** | **Av** (standard) eller **På** | Konfigurera [diagnostikloggning](../logic-apps/monitor-logic-apps-log-analytics.md) för logikappen med hjälp av [Azure Monitor-loggar](../log-analytics/log-analytics-overview.md). Kräver att du redan har en Log Analytics-arbetsyta. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen**. På så sätt visas logikappen automatiskt på din Azure-instrumentpanel och öppnas efter distributionen. Välj **Skapa**.

   > [!NOTE]
   > Om du inte vill fästa din logikapp måste du manuellt hitta och öppna logikappen så att du kan fortsätta.

   När Azure har distribuerat din logikapp öppnas Logic Apps Designer och visar en sida med en introduktionsvideo. 
   Under videon finns mallar för vanliga logikappmönster. 

5. Bläddra förbi introduktionsvideon och vanliga utlösare till **Mallar**. Välj en fördefinierad mall. Ett exempel:

   ![Välj en logikappmall](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Om du vill skapa logikappen från grunden väljer du **Tom Logikapp**.

   När du väljer en fördefinierad mall kan du visa mer information om mallen. 
   Ett exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Om du vill fortsätta med den markerade mallen väljer du **Använd den här mallen**. 

7. Baserat på kopplingarna i mallen uppmanas du att utföra något av följande steg:

   * Logga in med dina autentiseringsuppgifter på system eller tjänster som refereras av mallen.

   * Skapa anslutningar för alla tjänster eller system som refereras av mallen. Om du vill skapa en anslutning anger du ett namn för anslutningen och väljer vid behov den resurs som du vill använda. 

   * Om du redan har konfigurerat dessa anslutningar väljer du **Fortsätt**.

   Ett exempel:

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   När du är klar öppnas logikappen och visas i Logic Apps Designer.

   > [!TIP]
   > Om du vill återgå till mallvisaren väljer du **Mallar** i designerverktygsfältet. Den här åtgärden ignorerar alla ändringar som inte sparats, så ett varningsmeddelande visas för att bekräfta din begäran.

8. Fortsätt att bygga logikappen.

   > [!NOTE] 
   > Många mallar innehåller kopplingar som kanske redan har förifyllda obligatoriska egenskaper. Vissa mallar kan dock fortfarande kräva att du anger värden innan du kan distribuera logikappen på rätt sätt. Om du försöker distribuera utan att fylla i de saknade egenskapsfälten visas ett felmeddelande. 

## <a name="update-logic-apps-with-templates"></a>Uppdatera logikappar med mallar

1. I [Azure-portalen](https://portal.azure.com "Azure Portal")hittar och öppnar du logikappen i logikappdesignern.

2. Välj Mallar i **designerverktygsfältet**. Den här åtgärden ignorerar alla ändringar som inte sparats, så ett varningsmeddelande visas så att du kan bekräfta att du vill fortsätta. För att bekräfta väljer du **OK**. Ett exempel:

   ![Välj "Mallar"](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Bläddra förbi introduktionsvideon och vanliga utlösare till **Mallar**. Välj en fördefinierad mall. Ett exempel:

   ![Välj en logikappmall](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   När du väljer en fördefinierad mall kan du visa mer information om mallen. 
   Ett exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Om du vill fortsätta med den markerade mallen väljer du **Använd den här mallen**. 

5. Baserat på kopplingarna i mallen uppmanas du att utföra något av följande steg:

   * Logga in med dina autentiseringsuppgifter på system eller tjänster som refereras av mallen.

   * Skapa anslutningar för alla tjänster eller system som refereras av mallen. Om du vill skapa en anslutning anger du ett namn för anslutningen och väljer vid behov den resurs som du vill använda. 

   * Om du redan har konfigurerat dessa anslutningar väljer du **Fortsätt**.

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Logikappen öppnas och visas nu i Logic Apps Designer.

8. Fortsätt att bygga logikappen. 

   > [!TIP]
   > Om du inte har sparat ändringarna kan du ignorera ditt arbete och återgå till din tidigare logikapp. Välj **Ignorera**i designerverktygsfältet .

> [!NOTE] 
> Många mallar innehåller kopplingar som kanske redan har förifyllda obligatoriska egenskaper. Vissa mallar kan dock fortfarande kräva att du anger värden innan du kan distribuera logikappen på rätt sätt. Om du försöker distribuera utan att fylla i de saknade egenskapsfälten visas ett felmeddelande.

## <a name="deploy-logic-apps-built-from-templates"></a>Distribuera logikappar byggda från mallar

När du har gjort ändringarna i mallen kan du spara ändringarna. Den här åtgärden publicerar också logikappen automatiskt.

Välj **Spara** i designerverktygsfältet.

![Spara och publicera logikappen](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du skapar logikappar genom exempel, scenarier, kundberättelser och genomgångar.

> [!div class="nextstepaction"]
> [Granska exempel, scenarier och genomgångar av logikappar](../logic-apps/logic-apps-examples-and-scenarios.md)