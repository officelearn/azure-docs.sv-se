---
title: Exempel & vanliga scenarier – Azure Logic Apps
description: Exempel, scenarier, självstudier och genom gångar för Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: e06311bc-29eb-49df-9273-1f05bbb2395c
ms.date: 07/31/2019
ms.openlocfilehash: 000de22105615c3f6aa015b07e13bf8a47955b52
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706786"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Vanliga scenarier, exempel, självstudier och genom gångar för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att dirigera och integrera olika tjänster genom att tillhandahålla [hundratals färdiga anslutnings](../connectors/apis-list.md)program, från lokala SQL Server eller SAP till Azure Cognitive Services. Logic Appss tjänsten är "Server lös", så du behöver inte oroa dig för skalning eller instanser. Allt du behöver göra är att definiera arbets flödet med en utlösare och de åtgärder som arbets flödet utför. Den underliggande plattformen hanterar skalning, tillgänglighet och prestanda. Logic Apps är särskilt användbart för användnings fall och scenarier där du behöver koordinera flera åtgärder i flera system.

För att hjälpa dig att lära dig mer om många mönster och funktioner som Azure Logic Apps stöder, är här vanliga exempel och scenarier.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Populära start punkter för Logic app-arbetsflöden

Varje Logic app börjar med en [](../logic-apps/logic-apps-overview.md#logic-app-concepts)utlösare och bara en utlösare som startar ditt Logic app-arbetsflöde och skickar data som en del av den utlösaren. Vissa anslutningar tillhandahåller utlösare som kommer i följande typer:

* Avsöknings utlösare: Kontrollerar regelbundet en tjänst slut punkt för nya data. När det finns nya data skapar utlösaren och kör en ny arbets flödes instans med data som indata.

* *Push*-utlösare: Lyssnar efter data på en tjänst slut punkt och väntar tills en speciell händelse inträffar. När händelsen inträffar utlöses utlösaren direkt, vilket skapar och kör en ny arbets flödes instans som använder tillgängliga data som indata.

Här är några exempel på populära utlösare:

* Avsöknings

  * Med [ **upprepnings** ](../connectors/connectors-native-recurrence.md) utlösare kan du ange start datum och-tid plus upprepningen för att starta din Logic app. Du kan till exempel välja de vecko dagar och tidpunkter då din Logic-app ska initieras. Mer information finns i följande avsnitt:

    * [Schemalägg och kör återkommande automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Självstudier: Kontrol lera trafik enligt ett schema med Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Med utlösaren "när ett e-postmeddelande tas emot" kan din Logi Kap par söka efter nya e-postmeddelanden från en e-postprovider som stöds av Logic Apps, till exempel [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail](https://docs.microsoft.com/connectors/gmail/), [Outlook.com](https://docs.microsoft.com/connectors/outlook/)och så vidare. Mer information finns i följande avsnitt: 

    * [Självstudier: Hantera begär Anden om distributions lista med Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Självstudier: Automatisera hanteringen av e-postmeddelanden och bifogade filer med Azure Logic Apps](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **Http-** ](../connectors/connectors-native-http.md) utlösaren låter din Logic app kontrol lera en angiven tjänst slut punkt genom att kommunicera via http.
  
* Trycka

  * Med utlösaren för [ **förfrågningar** ](../connectors/connectors-native-reqres.md) kan din Logic app ta emot HTTP-förfrågningar och svara i real tid på händelser på något sätt.

  * [ **Http-webhook-** ](../connectors/connectors-native-webhook.md) utlösaren prenumererar på en tjänst slut punkt genom att registrera en återanrops- *URL* med den tjänsten. På så sätt kan tjänsten bara meddela utlösaren när den angivna händelsen inträffar, så att utlösaren inte behöver avsöka tjänsten.

När utlösaren har fått ett meddelande om nya data eller en händelse, skapar utlösaren en ny Logic app-arbetsflöde och kör åtgärderna i arbets flödet. Du kan komma åt alla data från utlösaren i hela arbets flödet. Utlösaren "på en ny tweet" skickar till exempel tweet-innehållet till Logic app-körningen. För att komma igång med Azure Logic Apps kan du försöka med följande snabb starts ämnen:

* [Snabbstart: Skapa ditt första automatiserade arbets flöde med Azure Logic Apps i Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snabbstart: Skapa automatiserade uppgifter, processer och arbets flöden med Azure Logic Apps med hjälp av Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snabbstart: Skapa och hantera automatiserade Logic app-arbetsflöden med hjälp av Visual Studio Code](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Svara på utlösare och utöka åtgärder

För system och tjänster som kanske inte har några publicerade anslutningar kan du även utöka Logic Apps.

* [Skapa anpassade utlösare eller åtgärder](../logic-apps/logic-apps-create-api-app.md)
* [Konfigurera tids krävande åtgärder för arbets flödes körningar](../logic-apps/logic-apps-create-api-app.md)
* [Svara på externa händelser och åtgärder med Webhooks](../logic-apps/logic-apps-create-api-app.md)
* [Anropa, utlösa eller kapsla arbets flöden med synkrona svar på HTTP-begäranden](../logic-apps/logic-apps-http-endpoint.md)
* [Självstudier: Bygg en AI-driven social instrument panel på några minuter med Logic Apps och Power BI](https://aka.ms/logicappsdemo)
* [Video: Svara på Twilio SMS Webhooks och skicka ett text svar](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Kontroll flöde, fel hantering och loggnings funktioner

Logic Apps innehåller omfattande funktioner för avancerat kontroll flöde, till exempel villkor, växlar, slingor och omfång. För att säkerställa elastiska lösningar kan du även implementera fel och undantags hantering i dina arbets flöden. För meddelande-och diagnostikloggar för arbets flödets körnings status Azure Logic Apps även övervaknings-och aviserings funktioner.

* Utföra olika åtgärder baserat på [villkors satser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [switch-instruktioner](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Upprepa steg eller bearbeta objekt i matriser och samlingar med slingor](../logic-apps/logic-apps-control-flow-loops.md)
* [Gruppera åtgärder tillsammans med omfattningar](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Författar fel och undantags hantering i ett arbets flöde](../logic-apps/logic-apps-exception-handling.md)
* [Användningsfall: Hur ett sjukvårds företag använder Logic app Exception-hantering för HL7 FHIR-arbetsflöden](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Aktivera övervakning, loggning och aviseringar för befintliga Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Aktivera övervakning och diagnostik-loggning när du skapar Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Distribuera och hantera Logic Apps

Du kan helt utveckla och distribuera Logi Kap par med Visual Studio, Azure DevOps eller någon annan käll kontroll och automatiserade build-verktyg. För att stödja distribution av arbets flöden och beroende anslutningar i en resurs mall använder Logic Apps mallar för Azure-resursanvändning. Visual Studio-verktyg genererar automatiskt de här mallarna, som du kan checka in till käll kontroll för versions hantering.

* [Skapa och distribuera Logic Apps med Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Aktivera övervakning, loggning och aviseringar för befintliga Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Automatisera Logic app-distribution](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Exempel: Ansluta till Azure Service Bus köer från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Ansluta till Azure Storage konton från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en Function-app-åtgärd för Azure Logic Apps och distribuera med Azure-pipeliner i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Ansluta till ett integrations konto från Azure Logic Apps och distribuera med Azure-pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Innehålls typer, konverteringar och omvandlingar i en körning

Du kan komma åt, konvertera och transformera flera innehålls typer med hjälp av de många funktionerna i språket för Azure Logic Apps [Workflow definition](https://aka.ms/logicappsdocs). Du kan till exempel konvertera mellan en sträng, JSON och XML med `@json()` -och `@xml()` -arbets flödes uttryck. Logic Appss motorn bevarar innehålls typer för att stödja innehålls överföring på ett icke-förstörande sätt mellan tjänster.

* [Hur arbets flödes uttryck fungerar i Logic Apps](../logic-apps/logic-apps-author-definitions.md)
* [Hantera innehålls typer som inte är JSON](../logic-apps/logic-apps-content-type.md), `application/xml`t `application/octet-stream`. ex., och`multipart/formdata`
* [Språk schema för arbets flödes definition för Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Andra integreringar och funktioner

Logic Apps erbjuder även integrering med många tjänster, t. ex. Azure Functions, Azure API Management, Azure App tjänster och anpassade HTTP-slutpunkter, till exempel REST och SOAP.

* [Skapa en social instrument panel i real tid med Azure-Server lös](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Anropa Azure Functions från Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Självstudier: Utlös Logic Apps med Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Självstudier: Övervaka ändringar av virtuella datorer med Azure Event Grid och Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Självstudier: Skapa en funktion som integreras med Azure Logic Apps och Azure Cognitive Services för att analysera Twitter-inlägg sentiment](../azure-functions/functions-twitter-email.md)
* [Självstudier: Övervakning och aviseringar för IoT-fjärrhantering med Azure Logic Apps du ansluter din IoT Hub och post lådan](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blogg: Anropa SOAP-slutpunkter från Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Slutpunkt-till-slutpunkt-scenarier

* [Rapporten Hantering av ärende hantering från slut punkt till slut punkt med Azure-tjänster, till exempel Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Kundberättelser

Lär dig hur Azure Logic Apps, tillsammans med andra Azure-tjänster och Microsoft-produkter, vilket hjälpte [dessa företag](https://aka.ms/logic-apps-customer-stories) att förbättra sin rörlighet och fokusera på sina kärn företag genom att förenkla, organisera, automatisera och dirigera komplexa processer.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [anslutningar för Logic Apps](../connectors/apis-list.md)
* Lär dig mer om [B2B Enterprise integration-scenarier med Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
