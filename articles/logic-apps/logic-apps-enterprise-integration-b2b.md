---
title: Exchange-meddelanden för B2B-scenarier för företagsintegrering
description: Ta emot och skicka B2B-meddelanden mellan handelspartner i Azure Logic Apps med hjälp av Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151198"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Ta emot och skicka B2B-meddelanden med hjälp av Azure Logic Apps och Enterprise Integration Pack

När du har ett integrationskonto som definierar handelspartner och avtal kan du skapa ett automatiserat arbetsflöde för företag (Business to Business) som utbyter meddelanden mellan handelspartner med hjälp av [Azure Logic Apps](../logic-apps/logic-apps-overview.md) med Enterprise Integration [Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps fungerar med kopplingar som stöder PROTOKOLL FRÅN AS2, X12, EDIFACT och RosettaNet. Du kan också kombinera dessa anslutningsappar med andra [anslutningsappar som är tillgängliga i Logic Apps,](../connectors/apis-list.md)till exempel Salesforce och Office 365 Outlook.

Den här artikeln visar hur du skapar en logikapp som tar emot en HTTP-begäran med hjälp av en begärandeutfärd, avkodar meddelandeinnehållet med hjälp av AS2- och X12-åtgärderna och returnerar sedan ett svar med hjälp av svarsåtgärden.

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte har en prenumeration ännu [registrerar du dig för ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En tom logikapp så att du kan skapa B2B-arbetsflödet med hjälp av [utlösaren Begäran](../connectors/connectors-native-reqres.md) som följs av dessa åtgärder:

  * [AS2 Avkoda](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Villkor](../logic-apps/logic-apps-control-flow-conditional-statement.md), som skickar ett [svar](../connectors/connectors-native-reqres.md) baserat på om åtgärden AS2 Avkod lyckas eller misslyckas

  * [Avkoda X12-meddelande](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Om du inte har tidigare i logikappar läser [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Ett [integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md) som är kopplat till din Azure-prenumeration och som är länkat till logikappen. Både logikappen och integrationskontot måste finnas på samma plats eller Azure-region.

* Minst två [handelspartner](../logic-apps/logic-apps-enterprise-integration-partners.md) som du redan har definierat i ditt integrationskonto tillsammans med [AS2- och X12-avtal](logic-apps-enterprise-integration-agreements.md) för dessa partner.

## <a name="add-request-trigger"></a>Lägga till utlösare för begäran

I det här exemplet används Logic App Designer i Azure-portalen, men du kan följa liknande steg för Logic App Designer i Visual Studio.

1. Öppna din tomma logikapp i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. I sökrutan anger `when a http request`du och väljer **När en HTTP-begäran tas emot** som ska användas som utlösare.

   ![Välj Begäran utlösare för att starta logikapp arbetsflöde](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Lämna rutan **Begäran json-schema** tom eftersom X12-meddelandet är en platt fil.

   ![Lämna "Begäran kropp JSON Schema" tom](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. När du är klar väljer du **Spara**i designerverktygsfältet .

   Det här steget genererar **HTTP POST-URL:en** som ska användas för att skicka begäran som utlöser logikappen. Om du vill kopiera webbadressen markerar du kopieringsikonen bredvid webbadressen.

   ![URL som genererats för begäran utlösare för att ta emot samtal](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Lägg till AS2-avkodningsåtgärd

Lägg nu till de B2B-åtgärder som du vill använda. I det här exemplet används AS2- och X12-åtgärder.

1. Under utlösaren väljer du **Nytt steg**. Om du vill dölja utlösarinformationen klickar du på utlösarens namnlist.

   ![Lägga till ytterligare ett steg i logikapparbetsflödet](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Ange **Choose an action**i sökrutan `as2 decode`under Välj en åtgärd och välj **AS2-avkodning (v2).**

   ![Hitta och välj "AS2 Avkoda (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Ange **den** indata som du vill att AS2-åtgärden ska avkoda, vilket är `body` innehållet som tas emot av HTTP-begäran. Du kan ange innehållet som indata på flera sätt, antingen från listan med dynamiskt innehåll eller som ett uttryck:

   * Om du vill välja från en lista som visar tillgängliga utlösarutdata klickar du i rutan **Meddelande för att avkoda.** När listan med dynamiskt innehåll visas under **När en HTTP-begäran tas emot**väljer du Egenskapsvärde för **Brödtext,** till exempel:

     ![Välj "Brödtext"-värde från utlösaren](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Om du vill ange ett uttryck `body` som refererar till utlösarens utdata klickar du i rutan **Meddelande för att avkoda.** När listan med dynamiskt innehåll visas väljer du **Uttryck**. I uttrycksredigeraren anger du uttrycket här och väljer **OK:**

     `triggerOutputs()['body']`

     Eller ange det här uttrycket direkt i rutan **Meddelande att avkoda:**

     `@triggerBody()`

     Uttrycket matchas **Body** till body-token.

     ![Löst utdata från utlösaren](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. För egenskapen **Meddelandehuvuden** anger du alla rubriker som krävs för `headers` AS2-åtgärden och som beskrivs av innehållet som tas emot av HTTP-begäran.

   Om du vill ange ett uttryck `headers` som refererar till utlösarens utdata klickar du i rutan **Meddelanderubriker.** När listan med dynamiskt innehåll visas väljer du **Uttryck**. I uttrycksredigeraren anger du uttrycket här och väljer **OK:**

   `triggerOutputs()['Headers']`

   Om du vill att det här uttrycket ska matchas som den här token växlar du mellan designer- och kodvyn, till exempel:

   ![Lösta rubriker från utlösaren](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Lägg till svarsåtgärd för meddelandeinleveransmeddelande

Om du vill meddela handelspartnern att meddelandet togs emot kan du returnera ett svar som innehåller ett MDN -meddelande (AS2 Message Disposition Notification) med hjälp av **svarsåtgärden.** Genom att lägga till den här åtgärden omedelbart efter **ÅTGÄRDEN AS2 Avcode,** om åtgärden misslyckas, fortsätter logikappen inte bearbetningen.

1. Under åtgärden **AS2 Avkoda** väljer du **Nytt steg**.

1. Välj **Inbyggd**under **Välj en åtgärd**. Skriv `condition` i sökrutan. Välj **Villkor**i listan **Åtgärder** .

   ![Lägg till åtgärden "Villkor"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Nu visas villkorsformen, inklusive banorna för om villkoret är uppfyllt eller inte.

   ![Villkorsform med beslutsbanor](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Ange nu villkoret att utvärdera. I rutan **Välj ett värde** anger du det här uttrycket:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   I mittenrutan kontrollerar du att jämförelseåtgärden är inställd på `is equal to`. Ange värdet `Expected`i rutan till höger . Om du vill att uttrycket ska matchas som den här token växlar du mellan designern och kodvyn.

   ![Villkorsform med beslutsbanor](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Ange nu svaren för att returnera om **ÅTGÄRDEN AS2-avkodning** lyckas eller inte.

   1. För det fall då **åtgärden AS2 Avkod** lyckas väljer du **Lägg till en åtgärd**i formen Om **true** . Ange **Choose an action**i sökrutan `response`under Välj en åtgärd och välj **Svar**.

      ![Sök efter och välj åtgärden "Svar"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Om du vill komma åt AS2 MDN från **AS2-avkodningens** utdata anger du följande uttryck:

      * Ange det här uttrycket i egenskapen **Svarsåtgärd:** **Response**

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Ange det här uttrycket i egenskapen **Brödtext** för svar: **Body**

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Om du vill att uttrycken ska matchas som token växlar du mellan designern och kodvyn:

      ![Matchat uttryck för åtkomst till AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. För det fall då **åtgärden AS2 Avkod** misslyckas väljer du **Lägg till en åtgärd**i den falska formen Om den är **falsk** . Ange **Choose an action**i sökrutan `response`under Välj en åtgärd och välj **Svar**. Ställ in åtgärden **Svar** för att returnera den status och det fel som du vill ha.

1. Spara din logikapp.

## <a name="add-decode-x12-message-action"></a>Lägg till åtgärden Avkoda X12-meddelande

1. Lägg nu till **meddelandeåtgärden Avkoda X12.** Under åtgärden **Svar** väljer du **Lägg till en åtgärd**.

1. Ange **Choose an action**i sökrutan `x12 decode`under Välj en åtgärd och välj **Avkoda X12-meddelande**.

   ![Sök efter och välj åtgärden "Avkoda X12-meddelande"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Om X12-åtgärden uppmanar dig att ange anslutningsinformation anger du namnet på anslutningen, väljer det integrationskonto som du vill använda och väljer sedan **Skapa**.

   ![Skapa X12-anslutning till integrationskonto](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Ange nu indata för X12-åtgärden. I det här exemplet används utdata från AS2-åtgärden, som är meddelandeinnehållet men observerar att innehållet är i JSON-objektformat och är base64 kodat. Så du måste konvertera det här innehållet till en sträng.

   I rutan **X12 Flat file message to decode** anger du det här uttrycket för att konvertera AS2-utdata:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Om du vill att uttrycket ska matchas som den här token växlar du mellan designern och kodvyn.

    ![Konvertera base64-kodat innehåll till en sträng](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Spara din logikapp.

   Om du behöver ytterligare steg för den här logikappen, till exempel för att avkoda meddelandeinnehållet och utdata som innehåll i JSON-objektformat, fortsätter du att skapa logikappen.

Du är nu klar med att konfigurera din B2B-logikapp. I en verklig app kanske du vill lagra de avkodade X12-data i en line-of-business app eller datalager. Se till exempel följande artiklar:

* [Ansluta till SAP-system från Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Övervaka, skapa och hantera SFTP-filer med hjälp av SSH- och Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Om du vill ansluta egna LOB-appar och använda dessa API:er i logikappen kan du lägga till fler åtgärder eller [skriva anpassade API:er](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Nästa steg

* [Ta emot och svara på inkommande HTTPS-samtal](../connectors/connectors-native-reqres.md)
* [Exchange AS2-meddelanden för integrering av B2B-företag](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Exchange X12-meddelanden för integrering av B2B-företag](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Läs mer om [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)