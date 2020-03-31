---
title: Exempel & vanliga scenarier
description: Hitta exempel, vanliga scenarier, självstudier och genomgångar för Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164635"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Vanliga scenarier, exempel, självstudier och genomgångar för Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) hjälper dig att orkestrera och integrera olika tjänster genom att tillhandahålla [hundratals färdiga kopplingar](../connectors/apis-list.md), allt från lokala SQL Server eller SAP till Azure Cognitive Services. Logic Apps-tjänsten är "serverlös", så du behöver inte oroa dig för skala eller instanser. Allt du behöver göra är att definiera arbetsflödet med en utlösare och de åtgärder som arbetsflödet utför. Den underliggande plattformen hanterar skala, tillgänglighet och prestanda. Logic Apps är särskilt användbart för användningsfall och scenarier där du behöver samordna åtgärder mellan flera system och tjänster.

För att hjälpa dig att lära dig mer om de funktioner och mönster som Azure Logic Apps stöder, beskriver den här artikeln vanliga startpunkter, exempel och scenarier.

## <a name="common-starting-points-for-logic-app-workflows"></a>Vanliga startpunkter för logikapparbetsflöden

Varje logikapp börjar med en [*utlösare*](../logic-apps/logic-apps-overview.md#logic-app-concepts)och endast en utlösare, som startar logikapparbetsflödet och skickar in alla data som en del av utlösaren. Vissa kopplingar ger utlösare, som finns i dessa typer:

* *Avsökningsutlösare*: Kontrollerar regelbundet en tjänstslutpunkt för nya data. När det finns nya data skapar och kör utlösaren en ny arbetsflödesinstans med data som indata.

* *Push-utlösare*: Lyssnar efter data vid en tjänstslutpunkt och väntar tills en viss händelse inträffar. När händelsen inträffar utlöses utlösaren omedelbart, skapa och köra en ny arbetsflödesinstans som använder tillgängliga data som indata.

Här är exempel som beskriver vanliga utlösare:

* *Polling* Avsökningsutlösare:

  * [ **Med återkommande** utlösare](../connectors/connectors-native-recurrence.md) kan du ställa in startdatum och starttid plus upprepning för att starta logikappen. Du kan till exempel välja veckodagar och tider på dygnet för att utlösa logikappen. Mer information finns i de här ämnena:<p>

    * [Schemalägg och kör återkommande automatiserade uppgifter, processer och arbetsflöden med Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Självstudiekurs: Skapa automatiserade, schemabaserade återkommande arbetsflöden med hjälp av Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * **När ett e-postmeddelande tas emot** kan logikappen söka efter ny e-post från alla e-postleverantörer som stöds av Logic Apps, till exempel Office [365 Outlook](../connectors/connectors-create-api-office365-outlook.md), [Gmail,](https://docs.microsoft.com/connectors/gmail/) [Outlook.com](https://docs.microsoft.com/connectors/outlook/)och så vidare. Mer information finns i de här ämnena:<p>

    * [Självstudiekurs: Skapa automatiska godkännandebaserade arbetsflöden med hjälp av Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Självstudiekurs: Automatisera uppgifter för att bearbeta e-postmeddelanden med hjälp av Azure Logic Apps, Azure Functions och Azure Storage](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * [ **HTTP-utlösaren** ](../connectors/connectors-native-http.md) kan anropa en tjänstslutpunkt via HTTP eller HTTPS. Mer information finns i [Anropa, utlösare eller kapsla arbetsflöden med hjälp av HTTP-slutpunkter](../logic-apps/logic-apps-http-endpoint.md).

* *Push* Tryckutlösare:

  * [ **Begäran-utlösaren** ](../connectors/connectors-native-reqres.md) kan ta emot inkommande HTTPS-begäranden.

  * [ **HTTP Webhook-utlösaren** ](../connectors/connectors-native-webhook.md) prenumererar på en tjänstslutpunkt genom att registrera en *motringnings-URL* med den tjänsten. På så sätt kan tjänsten bara meddela utlösaren när den angivna händelsen inträffar, så att utlösaren inte behöver avsöka tjänsten.

När den angivna händelsen inträffar utlöses utlösaren, vilket skapar en ny logikapparbetsflödesinstans och kör åtgärderna i arbetsflödet. Du kan komma åt alla data från utlösaren i hela arbetsflödet. Twitter **På en ny tweet-utlösare** skickar till exempel tweetinnehållet till logikappkörningen. Prova de här snabbstartsavsnitten för att komma igång med Azure Logic Apps:

* [Snabbstart: Skapa ditt första automatiserade arbetsflöde med hjälp av Azure Logic Apps - Azure-portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med hjälp av Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Snabbstart: Skapa och hantera automatiserade logikapparbetsflöden med hjälp av Visual Studio-kod](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>Funktioner för kontrollflöde och felhantering

Logikappar innehåller omfattande funktioner för avancerat kontrollflöde, till exempel villkor, växlar, loopar och scope. För att säkerställa motståndskraftiga lösningar kan du också implementera fel- och undantagshantering i dina arbetsflöden.

* Utföra olika åtgärder baserat på [villkorssatser](../logic-apps/logic-apps-control-flow-conditional-statement.md) och [växlingssatser](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Upprepa steg eller bearbeta objekt i matriser och samlingar med loopar](../logic-apps/logic-apps-control-flow-loops.md)
* [Gruppera åtgärder tillsammans med omfattningar](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Lägga till fel- och undantagshantering i ett arbetsflöde](../logic-apps/logic-apps-exception-handling.md)
* [Användningsfall: Hur ett vårdföretag använder undantagshantering för logikapp för HL7 FHIR-arbetsflöden](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Skapa anpassade API:er och kopplingar

För system och tjänster som inte har publicerade kopplingar kan du också utöka logikappar.

* [Skapa anpassade API:er för att anropa från Azure Logic Apps](../logic-apps/logic-apps-create-api-app.md)
* [Sök efter nya data eller händelser regelbundet med hjälp av avsökningsutlösarens mönster](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Vänta och lyssna efter nya data eller händelser med hjälp av webhook-utlösarmönstret](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Utföra tidskrävande uppgifter med hjälp av avsökningsåtgärdsmönstret](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Utföra tidskrävande uppgifter med hjälp av åtgärdsmönstret webhook](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Anpassade kopplingar i Azure Logic Apps](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Bygg upp B2B-lösningar (Business-to-Business)

För företagsintegreringslösningar och sömlös kommunikation mellan organisationer kan du skapa automatiserade skalbara arbetsflöden för dessa scenarier med hjälp av Enterprise Integration Pack (EIP) med Azure Logic Apps. Även om organisationer använder olika protokoll och format kan de utbyta meddelanden elektroniskt. EIP omvandlar olika format till ett format som dina organisationers system kan bearbeta och stöder branschstandardprotokoll, inklusive AS2, X12, EDIFACT och RosettaNet. För att skapa dessa lösningar skapar du ett integrationskonto, som är en separat Azure-resurs som tillhandahåller en säker, skalbar och hanterbar behållare för de artefakter som du definierar och använder med dina logikapparbetsflöden. Artefakter inkluderar till exempel handelspartner, avtal, kartor, scheman, certifikat och batchkonfigurationer.

* [Översikt: B2B företagsintegrationslösningar med Azure Logic Apps och Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Skapa och hantera integrationskonton för B2B-företagsintegreringar i Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Få tillgång till virtuella Azure-nätverksresurser

Ibland behöver dina logikappar och integrationskonton åtkomst till skyddade resurser, till exempel virtuella datorer (VMs) och andra system eller tjänster som finns i ett virtuellt Azure-nätverk. Om du vill konfigurera den här åtkomsten kan du skapa en integrationstjänstmiljö (ISE) där du kan skapa och köra dina logikappar. En ISE är en privat och isolerad instans av Logic Apps-tjänsten som använder dedikerade resurser som lagring och körs separat från den offentliga, "globala" Logic Apps-tjänsten med flera innehavare. Att separera din isolerade privata instans och den offentliga globala instansen bidrar också till att minska den inverkan som andra Azure-klienter kan ha på dina appars prestanda, som också kallas "bullriga grannar"-effekten.

* [Översikt: Åtkomst till virtuella Azure-nätverksresurser från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Ansluta till virtuella Azure-nätverk från Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Distribuera, hantera och övervaka logikappar

Du kan fullt ut utveckla och distribuera logikappar med Visual Studio, Azure DevOps eller andra källkontroll- och automatiserade byggverktyg. För att stödja distribution för arbetsflöden och beroende anslutningar i en resursmall använder logikappar Azure-resursdistributionsmallar. Visual Studio-verktyg genererar automatiskt dessa mallar, som du kan checka in på källkontrollen för versionshantering. För meddelande- och diagnostikloggar för arbetsflödeskörningsstatus tillhandahåller Azure Logic Apps också övervakning och aviseringar.

### <a name="deploy"></a>Distribuera

* [Snabbstart: Skapa automatiserade uppgifter, processer och arbetsflöden med hjälp av Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Översikt: Automatisera distribution av logikappar](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Skapa Azure Resource Manager-mallar för att automatisera distributionen för Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Distribuera Azure Resource Manager-mallar för Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Exempel: Ansluta till Azure Service Bus-köer från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till Azure Storage-konton från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Konfigurera en funktionsappåtgärd för Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exempel: Anslut till ett integrationskonto från Azure Logic Apps och distribuera med Azure Pipelines i Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Exempel: Dirigera Azure-pipelines med hjälp av Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Hantera

* [Hantera logikappar med Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Skapa och hantera integrationskonton för B2B-företagsintegrationer](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Hantera integrationstjänstmiljö (ISE) i Azure Logic Apps](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Övervaka

* [Övervaka körstatus, granska utlösarhistorik och konfigurera aviseringar för Azure Logic Apps](../logic-apps/monitor-logic-apps.md)
* [Konfigurera Azure Monitor-loggar och samla in diagnostikdata för Azure Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Konfigurera Azure Monitor-loggar och samla in diagnostikdata för B2B-meddelanden i Azure Logic Apps](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Visa och skapa frågor för övervakning och spårning i Azure Monitor-loggar för Azure Logic Apps](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Hantera innehållstyper, konverteringar och omvandlingar

Du kan komma åt, konvertera och omvandla flera innehållstyper med hjälp av de många funktionerna i [arbetsflödesdefinitionsspråket](https://aka.ms/logicappsdocs)för Azure Logic Apps . Du kan till exempel konvertera mellan en sträng, `@json()` JSON och XML med uttrycken och `@xml()` arbetsflödesuttrycken. Logic Apps-motorn bevarar innehållstyper för att stödja innehållsöverföring på ett förlustfritt sätt mellan tjänster.

* [Hantera innehållstyper i Azure](../logic-apps/logic-apps-content-type.md) `application/`Logic `application/octet-stream`Apps , till exempel , och`multipart/formdata`
* [Referensguide till att använda funktioner i uttryck för Azure Logic Apps och Power Automate](../logic-apps/workflow-definition-language-functions-reference.md)
* [Språkschema för arbetsflödesdefinition för Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Andra integrationer och funktioner

Azure Logic Apps integreras med många tjänster, till exempel Azure Functions, Azure API Management, Azure App Service och anpassade HTTP-slutpunkter, till exempel REST och SOAP.

* [Anropa Azure-funktioner från Azure Logic Apps](../logic-apps/logic-apps-azure-functions.md)
* [Självstudiekurs: Anropa eller utlösa logikappar med hjälp av Azure Functions och Azure Service Bus](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Självstudiekurs: Skapa en instrumentpanel för kundinsikter för direktuppspelning med Azure Logic Apps och Azure Functions](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Självstudiekurs: Skapa en funktion som integreras med Azure Logic Apps och Azure Cognitive Services för att analysera Twitter-inläggssentiment](../azure-functions/functions-twitter-email.md)
* [Självstudiekurs: Skapa en AI-driven social instrumentpanel med hjälp av Power BI- och Azure Logic Apps](https://aka.ms/logicappsdemo)
* [Självstudiekurs: Övervaka ändringar av virtuella datorer med hjälp av Azure Event Grid och Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Självstudiekurs: IoT fjärrövervakning och meddelanden med Azure Logic Apps som ansluter din IoT-hubb och postlåda](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blogg: Anropa SOAP-tjänster med hjälp av Azure Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Slutpunkt-till-slutpunkt-scenarier

* [Faktablad: Integrering av heltäckande ärendehantering med Azure-tjänster, till exempel Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Kundberättelser

Lär dig mer om hur Azure Logic Apps, tillsammans med andra Azure-tjänster och Microsoft-produkter, hjälpte [dessa företag](https://aka.ms/logic-apps-customer-stories) att förbättra sin smidighet och fokusera på sina kärnföretag genom att förenkla, organisera, automatisera och iscensätta komplexa processer.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [kopplingar för Logic Apps](../connectors/apis-list.md)
* Lär dig mer om [scenarier för integrering av B2B-företag med Azure Logic Apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
