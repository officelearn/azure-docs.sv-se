---
title: Skapa arbetsflöden från mallar – Azure Logic Apps | Microsoft Docs
description: Skapa arbetsflöden snabbare med hjälp av mallar för logikappar i Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.date: 10/15/2017
ms.openlocfilehash: 134a8f9625b45a8196ebd47f10286093f6ba0d46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459693"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Skapa logikappsarbetsflöden från fördefinierade mallar

Om du vill komma igång med att skapa arbetsflöden snabbt innehåller Logic Apps-mallar, som är färdiga logic apps som följer vanliga mönster. Använda mallarna enligt eller redigera dem så att de passar din situation.

Här följer några mallkategorier:

| Malltyp | Beskrivning | 
| ------------- | ----------- | 
| Företagsmallar i molnet | För att integrera Azure Blob, Dynamics CRM, Salesforce, Box, och innehåller andra anslutningsappar för ditt företagsmoln behöver. Du kan till exempel använda mallarna för att organisera företag leads eller säkerhetskopiera dina data i företagsnätverket. | 
| Personliga produktivitetsmallar | Förbättra personliga produktivitet genom att ange dagliga påminnelser, att aktivera viktiga arbetsobjekt till att göra-listor och automatiserar tidskrävande uppgifter till godkännandesteg för en enskild användare. | 
| Konsumenten molnet mallar | För integrering av sociala tjänster, till exempel Twitter, Slack, och e-post. Användbart för att stärka sociala medier marknadsföringskampanjer. Dessa mallar kan också innehålla uppgifter, till exempel molnet kopierar, vilket ökar produktiviteten genom att spara tid på traditionellt återkommande uppgifter. | 
| Enterprise integration pack mallar | För att konfigurera en VETER (validera, extrahera, transformera, berika, dirigera) pipelines, ta emot en X12 EDI-dokument via AS2 och Transformera till XML och hantering av X12, EDIFACT, AS2-meddelanden och. | 
| Protocol-mallar som mönster | För att implementera protokollet mönster, till exempel begäranden och svar via HTTP och integreringar över FTP- och SFTP. Använda mallarna som tillhandahålls eller skapa på dem för komplexa protokollet mönster. | 
||| 

Om du inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. Läs mer om hur du skapar en logikapp, [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Skapa logic apps från mallar

1. Om du inte redan gjort loggar du in den [Azure-portalen](https://portal.azure.com "Azure-portalen").

2. Välj **Skapa en resurs** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Skapa din logikapp med inställningarna i tabellen under den här bilden:

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *namn-på-Azure-prenumerationen* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *namn-på-Azure-resursgruppen* | Skapa eller välj en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) för den här logikappen och organisera alla resurser som är associerade med den här appen. | 
   | **Plats** | *din-Azure-datacenter-region* | Välj datacenterregion för att distribuera logikappen, till exempel USA, västra. | 
   | **Log Analytics** | **Inaktivera** (standard) eller **på** | Aktivera [diagnostikloggning](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) för din logikapp via [Azure Monitor loggar](../log-analytics/log-analytics-overview.md). Kräver att du redan har en Log Analytics-arbetsyta. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen**. På så sätt visas logikappen automatiskt på din Azure-instrumentpanel och öppnas efter distributionen. Välj **Skapa**.

   > [!NOTE]
   > Om du inte vill fästa din logikapp måste du manuellt hitta och öppna logikappen så att du kan fortsätta.

   När Azure har distribuerat din logikapp öppnas Logic Apps Designer och visar en sida med en introduktionsvideo. 
   Under videon finns mallar för vanliga logikappmönster. 

5. Rulla förbi introduktion och vanliga utlösare till **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välja en mall för logikapp](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > För att skapa din logikapp från början, Välj **tom Logikapp**.

   När du väljer en fördefinierad mall kan visa du mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Om du vill fortsätta med den valda mallen, Välj **Använd den här mallen**. 

7. Baserat på anslutningsapparna i mallen, uppmanas du att utföra de här stegen:

   * Logga in med dina autentiseringsuppgifter för att system eller tjänster som refereras av mallen.

   * Skapa anslutningar för någon tjänst eller ett system som refereras av mallen. Ange ett namn för anslutningen för att skapa en anslutning, och om det behövs väljer du den resurs som du vill använda. 

   * Om du redan har konfigurerat dessa anslutningar, väljer **Fortsätt**.

   Exempel:

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   När du är klar ser logikappen öppnas och visas i Logic Apps Designer.

   > [!TIP]
   > Om du vill återgå till mallen visningsprogrammet, Välj **mallar** designerverktygsfältet. Den här åtgärden tar bort alla osparade ändringar, så att ett varningsmeddelande visas som bekräftar din förfrågan.

8. Fortsätt att skapa din logikapp.

   > [!NOTE] 
   > Många mallar innehåller kopplingar som kanske redan har innehåller nödvändiga egenskaper. En del mallar kan dock fortfarande kräver att du anger värden innan du kan distribuera logikappen korrekt. Om du försöker distribuera utan att fylla i egenskapsfälten saknas, får du ett felmeddelande. 

## <a name="update-logic-apps-with-templates"></a>Uppdatera logikappar med mallar

1. I den [Azure-portalen](https://portal.azure.com "Azure-portalen"), hitta och öppna logikappen i th Logic App Designer.

2. I verktygsfältet för appdesignern väljer **mallar**. Den här åtgärden tar bort alla osparade ändringar, så att ett varningsmeddelande visas så att du kan bekräfta att du vill fortsätta. Bekräfta genom att välja **OK**. Exempel:

   ![Välj ”mallar”](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Rulla förbi introduktion och vanliga utlösare till **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välja en mall för logikapp](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   När du väljer en fördefinierad mall kan visa du mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Om du vill fortsätta med den valda mallen, Välj **Använd den här mallen**. 

5. Baserat på anslutningsapparna i mallen, uppmanas du att utföra de här stegen:

   * Logga in med dina autentiseringsuppgifter för att system eller tjänster som refereras av mallen.

   * Skapa anslutningar för någon tjänst eller ett system som refereras av mallen. Ange ett namn för anslutningen för att skapa en anslutning, och om det behövs väljer du den resurs som du vill använda. 

   * Om du redan har konfigurerat dessa anslutningar, väljer **Fortsätt**.

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Din logikapp nu öppnas och visas i Logic Apps Designer.

8. Fortsätt att skapa din logikapp. 

   > [!TIP]
   > Om du inte har sparat ändringarna kan du ignorera ändringarna och återgå till logikappen tidigare. I verktygsfältet för appdesignern väljer **Ignorera**.

> [!NOTE] 
> Många mallar innehåller kopplingar som kanske har redan ifyllda på förhand nödvändiga egenskaper. En del mallar kan dock fortfarande kräver att du anger värden innan du kan distribuera logikappen korrekt. Om du försöker distribuera utan att fylla i egenskapsfälten saknas, får du ett felmeddelande.

## <a name="deploy-logic-apps-built-from-templates"></a>Distribuera logic apps från mallar

När du har gjort dina ändringar i mallen kan du spara dina ändringar. Den här åtgärden publicerar också automatiskt din logikapp.

Välj **Spara** i designerverktygsfältet.

![Spara och publicera din logikapp](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Läs mer om att skapa logikappar via exempel, scenarier, kundberättelser och genomgångar.

> [!div class="nextstepaction"]
> [Granska logic app-exempel, scenarier och genomgångar](../logic-apps/logic-apps-examples-and-scenarios.md)