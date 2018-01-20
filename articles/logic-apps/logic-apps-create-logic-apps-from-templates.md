---
title: "Skapa arbetsflöden från mallar - Azure Logic Apps | Microsoft Docs"
description: "Skapa arbetsflöden snabbare med hjälp av logic app mallar"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 49b4bbfda4518b03ef6080bec1e2a493933af4f5
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Skapa logik app arbetsflöden från fördefinierade mallar

Om du vill komma igång snabbt skapa arbetsflöden, innehåller Logic Apps mallar, som är färdiga logic apps som följer vanliga mönster. Använda mallarna som eller redigera dem så att de passar din situation.

Här följer några mallkategorier:

| Malltypen | Beskrivning | 
| ------------- | ----------- | 
| Företagsmallar i molnet | För att integrera Azure Blob, Dynamics CRM, Salesforce rutan samt övriga kopplingar för ditt företag moln måste. Du kan till exempel använda mallarna för att organisera business leads eller säkerhetskopiera filen för företagets data. | 
| Personlig produktivitet mallar | Förbättra personlig produktivitet genom att ange dagliga påminnelser, aktivera viktiga arbetsobjekt i att göra-listor och automatiskt tidskrävande uppgifter till en enskild användare steg. | 
| Konsumenten molnet mallar | För att integrera tjänster för social media, till exempel Twitter, Slack och e-post. Användbart för att stärka sociala medier marknadsföringskampanjer. Dessa mallar kan också innehålla uppgifter, till exempel moln kopierar, vilket ökar produktiviteten genom att spara tid på traditionellt återkommande uppgifter. | 
| Företagsmallar integration pack | För att konfigurera VETER (validera, extrahera, transformera, utöka, vidarebefordra) pipelines, tar emot en X12 EDI-dokument över AS2 och omvandla till XML och hantering av X12 EDIFACT, och AS2-meddelanden. | 
| Protokollet mönster mallar | För att implementera protokollet mönster, till exempel begäran-svar via HTTP och integreringar över FTP- och SFTP. Använd dessa mallar enligt eller bygga på dem för komplexa protokollet mönster. | 
||| 

Om du inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar. Mer information om hur du skapar en logikapp finns [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Skapa logikappar från mallar

1. Om du inte redan gjort logga in på den [Azure-portalen](https://portal.azure.com "Azure-portalen").

2. Välj **Ny** > **Enterprise-integration** > **Logikapp** på Azure-huvudmenyn.

   ![Azure Portal, Ny, Enterprise-integration, Logikapp](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Skapa din logikapp med inställningarna i tabellen under den här bilden:

   ![Tillhandahålla information om logikappar](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Inställning | Värde | Beskrivning | 
   | ------- | ----- | ----------- | 
   | **Namn** | *logikappens-namn* | Ange ett unikt namn för logikappen. | 
   | **Prenumeration** | *namn-på-Azure-prenumerationen* | Ange den prenumeration som du vill använda. | 
   | **Resursgrupp** | *namn-på-Azure-resursgruppen* | Skapa eller välj en [Azure-resursgrupp](../azure-resource-manager/resource-group-overview.md) för den här logikapp och för att organisera alla resurser som är associerade med den här appen. | 
   | **Plats** | *din-Azure-datacenter-region* | Välj datacenterregion för att distribuera logikappen, till exempel USA, västra. | 
   | **Log Analytics** | **Inaktivera** (standard) eller **på** | Aktivera [diagnostikloggning](../logic-apps/logic-apps-monitor-your-logic-apps.md#turn-on-diagnostics-logging-for-your-logic-app) för din logikapp via [Azure logganalys](../log-analytics/log-analytics-overview.md). Kräver att du redan har en [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) arbetsytan. | 
   |||| 

4. När du är klar väljer du **Fäst på instrumentpanelen**. På så sätt visas logikappen automatiskt på din Azure-instrumentpanel och öppnas efter distributionen. Välj **Skapa**.

   > [!NOTE]
   > Om du inte vill fästa din logikapp måste du manuellt hitta och öppna logikappen så att du kan fortsätta.

   När Azure har distribuerat din logikapp öppnas Logic Apps Designer och visar en sida med en introduktionsvideo. 
   Under videon finns mallar för vanliga logikappmönster. 

5. Rulla förbi introduktion video och vanliga utlösare så **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välj en mall för logik-app](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > Om du vill skapa din logikapp från början, Välj **tom Logikapp**.

   När du väljer en fördefinierad mall kan visa du mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. Om du vill fortsätta med den valda mallen väljer **använda mallen för**. 

7. Baserat på kopplingar i mallen, uppmanas du att utföra de här stegen:

   * Logga in med dina autentiseringsuppgifter till datorer eller tjänster som refereras av mallen.

   * Skapa anslutningar för några tjänster eller system som refereras av mallen. Ange ett namn för din anslutning och om det behövs, Välj den resurs som du vill använda för att skapa en anslutning. 

   * Om du redan har konfigurerat dessa anslutningar, väljer du **Fortsätt**.

   Exempel:

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   När du är klar logikappen öppnas och visas i designern för Logic Apps.

   > [!TIP]
   > Om du vill återgå till mallen visningsprogrammet väljer **mallar** i verktygsfältet designer. Den här åtgärden tar bort alla osparade ändringar så visas en varning att bekräfta din begäran.

8. Fortsätta skapa din logikapp.

   > [!NOTE] 
   > Många mallar innehåller kopplingar som kanske redan har förinställd nödvändiga egenskaper. Vissa mallar kan dock fortfarande kräver att du anger värden innan du kan distribuera logikappen korrekt. Om du försöker distribuera utan att slutföra saknade egenskapsfält får ett felmeddelande. 

## <a name="update-logic-apps-with-templates"></a>Uppdatera logikappar med mallar

1. I den [Azure-portalen](https://portal.azure.com "Azure-portalen"), hitta och öppna logikappen i th logik App Designer.

2. Välj verktygsfältet designer **mallar**. Den här åtgärden tar bort alla osparade ändringar så visas en varning så att du kan bekräfta att du vill fortsätta. Bekräfta genom att välja **OK**. Exempel:

   ![Välj ”mallar”](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Rulla förbi introduktion video och vanliga utlösare så **mallar**. Välj en fördefinierad mall. Exempel:

   ![Välj en mall för logik-app](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   När du väljer en fördefinierad mall kan visa du mer information om mallen. 
   Exempel:

   ![Välj en fördefinierad mall](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. Om du vill fortsätta med den valda mallen väljer **använda mallen för**. 

5. Baserat på kopplingar i mallen, uppmanas du att utföra de här stegen:

   * Logga in med dina autentiseringsuppgifter till datorer eller tjänster som refereras av mallen.

   * Skapa anslutningar för några tjänster eller system som refereras av mallen. Ange ett namn för din anslutning och om det behövs, Välj den resurs som du vill använda för att skapa en anslutning. 

   * Om du redan har konfigurerat dessa anslutningar, väljer du **Fortsätt**.

   ![Skapa anslutningar](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Din logikapp nu öppnas och visas i designern för Logic Apps.

8. Fortsätta skapa din logikapp. 

   > [!TIP]
   > Om du inte har sparat ändringarna kan du ignorera ändringarna och återgå till logikappen tidigare. Välj verktygsfältet designer **Ignorera**.

> [!NOTE] 
> Många mallar innehåller kopplingar som kanske redan inför ifyllda nödvändiga egenskaper. Vissa mallar kan dock fortfarande kräver att du anger värden innan du kan distribuera logikappen korrekt. Om du försöker distribuera utan att slutföra saknade egenskapsfält får ett felmeddelande.

## <a name="deploy-logic-apps-built-from-templates"></a>Distribuera logikappar som skapas från mallar

När du har gjort ändringarna i mallen kan du spara ändringarna. Den här åtgärden publicerar automatiskt din logikapp.

Välj **Spara** i designerverktygsfältet.

![Spara och publicera din logikapp](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Lär dig att skapa logikappar via exempel, scenarier, kund artiklar och genomgång.

> [!div class="nextstepaction"]
> [Granska logik app exempel och scenarier genomgång](../logic-apps/logic-apps-examples-and-scenarios.md)