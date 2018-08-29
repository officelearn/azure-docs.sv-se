---
title: Exempel och vanliga scenarier – Azure Logic Apps | Microsoft Docs
description: Exempel, scenarier, självstudier och genomgångar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 01/31/18
ms.openlocfilehash: f35822351a1ff5176548d67c8d94ada02f470421
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43123716"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Vanliga scenarier, exempel, självstudier och genomgångar för Azure Logic Apps

[Med Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att samordna och integrera olika tjänster genom att tillhandahålla [100 + färdiga att använda kopplingar](../connectors/apis-list.md)sträcker sig från en lokal SQLServer eller SAP för Microsoft Cognitive Services. Logic Apps-tjänsten är ”serverlös”, så att du inte behöver bekymra dig om att skala eller instanser. Allt du behöver göra är att definiera arbetsflödet med en utlösare och åtgärder som arbetsflödet utför. Den underliggande plattformen hanterar skalbarhet, tillgänglighet och prestanda. Logic Apps är särskilt användbart för scenarier där du vill samordna flera åtgärder över flera system och användningsfall.

För att lära dig mer om många mönster och funktioner som [Azure Logic Apps](../logic-apps/logic-apps-overview.md) stöder, här är vanliga exempel och scenarier.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Vanliga startpunkter för arbetsflöden i logikappar

Varje logikapp börjar med en [ *utlösaren*](../logic-apps/logic-apps-overview.md#logic-app-concepts), och bara en utlösare som startar logikappens arbetsflöde och skickar alla data som en del av den utlösaren. Flera anslutningsappar innehåller utlösare, som finns dessa typer av:

* *Sökningsutlösare*: regelbundet kontrollerar om en tjänstslutpunkt för nya data. När det finns nya data, skapar utlösaren och kör en ny arbetsflödesinstans med data som indata.

* *Push-utlösare*: lyssnar efter data på en tjänstslutpunkt och väntar tills en viss händelse inträffar. När händelsen inträffar utlöses utlösaren omedelbart, skapa och köra en ny arbetsflödesinstans som använder alla tillgängliga data som indata.

Här följer några exempel på populära utlösare:

* Avsökningsintervall: 

  * [**Schema – återkommande** utlösaren](../connectors/connectors-native-recurrence.md) kan du ange startdatum och tid plus upprepningen för aktiveringen din logikapp. 
  Du kan till exempel välja dagar i veckan och tidpunkter på dagen för att utlösa logikappen.

  * ”När ett e-postmeddelande tas emot” utlösaren låter din logikapp Kontrollera för nya e-post från alla e postleverantör som stöds av Logic Apps, till exempel [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [ Outlook.com](https://docs.microsoft.com/connectors/outlook/)och så vidare.

  * Den [ **HTTP** utlösaren](../connectors/connectors-native-http.md) låter din logikapp Kontrollera en slutpunkt för angivna genom att kommunicera via HTTP.
  
* Push:

  * Den [ **begäran / svar - begäran** utlösaren](../connectors/connectors-native-reqres.md) kan logikappen tar emot HTTP-begäranden och svarar i realtid på händelser på något sätt.

  * Den [ **HTTP-Webhook** utlösaren](../connectors/connectors-native-webhook.md) prenumererar på en tjänstslutpunkt genom att registrera en *Motringnings-URL för* med den tjänsten. 
  På så sätt kan tjänsten kan bara meddela utlösaren när den angivna händelsen inträffar, så att utlösaren inte behöver avsöker tjänsten.

När du har fått ett meddelande om nya data eller en händelse utlösaren utlöses skapar en ny logikappinstans arbetsflöde och kör åtgärderna i arbetsflödet. Du kan komma åt data från utlösare i hela arbetsflödet. Till exempel skickar utlösaren ”på en ny tweet” tweet-innehåll till den logikapp-körningen. 

## <a name="respond-to-triggers-and-extend-actions"></a>Reagera på utlösare och utöka åtgärder

Du kan också utöka logikappar för system och tjänster som inte kanske har publicerats kopplingar.

* [Skapa anpassade utlösare eller åtgärder](../logic-apps/logic-apps-create-api-app.md)
* [Konfigurera långvariga åtgärder för arbetsflödeskörningar](../logic-apps/logic-apps-create-api-app.md)
* [Svara på externa händelser och åtgärder med webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Anropa, utlösare, eller kapsla arbetsflöden med synkron svar på HTTP-begäranden](../logic-apps/logic-apps-http-endpoint.md)
* [Självstudie: Skapa en social instrumentpanel med AI-driven på några minuter med Logic Apps och Power BI](http://aka.ms/logicappsdemo)
* [Video: Svara på Twilio-SMS webhooks och skicka ett textsvar](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Kontrollflöde och felhantering loggningsfunktioner

Logic apps innehåller omfattande funktioner för avancerad Kontrollflöde som villkor, växlar, slingor och omfång. För att säkerställa elastiska lösningar, kan du också implementera fel och undantagshantering i dina arbetsflöden. För meddelandet och diagnostikloggar för arbetsflödeskörningen status, ger Azure Logic Apps också övervakning och aviseringar.

* Utföra olika åtgärder baserat på [villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [switch-satser](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Upprepa steg eller processen objekt i matriser och samlingar med slingor](../logic-apps/logic-apps-control-flow-loops.md)
* [Gruppåtgärder tillsammans med omfattningar](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Författare fel och undantagshantering i ett arbetsflöde](../logic-apps/logic-apps-exception-handling.md)
* [Användningsfall: hur vårdinrättningar ska använder logic app undantagshantering för HL7 FHIR-arbetsflöden](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Aktivera övervakning, loggning och aviseringar för befintliga logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Aktivera övervakning och diagnostikloggning när du skapar logikappar](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Distribuera och hantera logikappar

Du kan helt utveckla och distribuera logic apps med Visual Studio, Visual Studio Team Services eller andra källkontroll och automatisk build-verktyg. För att stödja distribution för arbetsflöden och beroende anslutningar i en resursmall för, Använd logikappar mallar för distribution av Azure-resurs. Visual Studio-verktyg kan du automatiskt generera dessa mallar som du kan checka in till källkontroll för versionshantering.

* [Skapa och distribuera logic apps i Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Aktivera övervakning, loggning och aviseringar för befintliga logikappar](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Skapa en mall för automatisk distribution](../logic-apps/logic-apps-create-deploy-template.md)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Typer av innehåll, konverteringar och transformationer inom en körning

Du kan komma åt, konvertera och transformera flera typer av innehåll med hjälp av de många funktionerna i Azure Logic Apps [definitionsspråk för arbetsflödet](http://aka.ms/logicappsdocs). Exempel: du kan konvertera mellan en sträng, JSON och XML med den `@json()` och `@xml()` arbetsflöde uttryck. Logic Apps-motorn bevarar innehållstyper för att stödja innehållet i en förlustfri sätt mellan tjänster.

* [Så här fungerar uttryck för arbetsflödet i logikappar](../logic-apps/logic-apps-author-definitions.md)
* [Hantera innehållstyper för icke-JSON](../logic-apps/logic-apps-content-type.md), till exempel `application/xml`, `application/octet-stream`, och `multipart/formdata`
* [Arbetsflöde för arbetsflödesschema för Azure Logic Apps](http://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Andra funktioner och integreringar

Logikappar ger även integrering med många tjänster, till exempel Azure Functions, Azure API Management, Azure App Services och anpassade HTTP-slutpunkter, till exempel REST och SOAP.

* [Skapa en social instrumentpanel i realtid med Azure utan Server](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Anropa Azure-funktioner från logikappar](../logic-apps/logic-apps-azure-functions.md)
* [Självstudie: Utlösaren logic apps med Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Självstudie: Övervaka ändringar av virtuell dator med Azure Event Grid och Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Självstudier: Skapa en funktion som kan integreras med Azure Logic Apps och Microsoft Cognitive Services för att analysera sentiment för Twitter-inlägg](../azure-functions/functions-twitter-email.md)
* [Självstudie: IoT fjärrövervakning och aviseringar med Azure Logic Apps ansluter dina IoT-hubb och postlåda](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blogg: Anropa SOAP-slutpunkter från logikappar](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Slutpunkt-till-slutpunkt-scenarier

* [White Paper: Slutpunkt till slutpunkt fallhantering integrering med Azure-tjänster, till exempel Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Kundberättelser

Lär dig hur Azure Logic Apps, tillsammans med andra Azure-tjänster och Microsoft-produkter, hjälpte [dessa företag](https://aka.ms/logic-apps-customer-stories) förbättra sin rörlighet och fokusera på sina core företag genom att förenkla, ordna, automatisera och samordna komplexa processer.

## <a name="next-steps"></a>Nästa steg

* [Skapa logikappsdefinitioner med JSON](../logic-apps/logic-apps-author-definitions.md)
* [Hantera fel och undantag i logic apps](../logic-apps/logic-apps-exception-handling.md)
* [Skicka dina kommentarer, frågor, feedback eller förslag för att förbättra Azure Logic Apps](https://feedback.azure.com/forums/287593-logic-apps)