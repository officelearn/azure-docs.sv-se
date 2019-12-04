---
title: Skapa B2B Enterprise-integrationer
description: Ta emot B2B-data i Azure Logic Apps med Enterprise-integrationspaket
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/08/2016
ms.openlocfilehash: 39966b8171296a8608b9436485f7682d114c8410
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793093"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Ta emot B2B-data med Azure Logic Apps och Enterprise-integrationspaket

När du har skapat ett integrations konto som har partner och avtal, är du redo att skapa ett Business to business-arbetsflöde (B2B) för din Logic app med [Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Krav

Om du vill använda åtgärderna AS2 och X12 måste du ha ett Enterprise-integration-konto. Lär dig [hur du skapar ett Enterprise-integration-konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Skapa en Logic app med B2B-kopplingar

Följ de här stegen för att skapa en B2B-Logic-app som använder AS2-och X12-åtgärder för att ta emot data från en handels partner:

1. Skapa en Logic-app och [Länka sedan din app till ditt integrations konto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Lägg till en **begäran – när en HTTP-begäran tas emot** som en utlösare till din Logic app.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Om du vill lägga till åtgärden **avkoda AS2** väljer du **Lägg till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Om du vill filtrera alla åtgärder till den som du vill använda anger du ordet **AS2** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Välj åtgärden **AS2-avkoda AS2-meddelande** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Lägg till den **text** som du vill använda som inmatad. 
   I det här exemplet väljer du innehållet i HTTP-begäran som utlöser den logiska appen. Eller ange ett uttryck som indata för sidhuvuden i fältet **rubriker** :

    @triggerOutputs() [' headers ']

7. Lägg till de nödvändiga **rubrikerna** för AS2, som du hittar i rubrikerna för http-begäran. 
   I det här exemplet väljer du rubrikerna för HTTP-begäran som utlöser den logiska appen.

8. Lägg nu till åtgärden avkoda X12-meddelande. Välj **Lägg till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Om du vill filtrera alla åtgärder till den som du vill använda anger du ordet **x12** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Välj åtgärden **X12-avkoda X12-meddelande** .

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nu måste du ange indatamängden för den här åtgärden. 
    Dessa indata är utdata från föregående AS2-åtgärd.

    Det faktiska meddelande innehållet finns i ett JSON-objekt och base64-kodat, så du måste ange ett uttryck som indatamängden. 
    Ange följande uttryck i det **X12 platt fil meddelandet för att avkoda** indatafilen:
    
    @base64ToString(brödtext (' Decode_AS2_message ')? [' AS2Message']? [' Content '])

    Nu kan du lägga till steg för att avkoda de X12-data som tagits emot från handels partnern och utgående objekt i ett JSON-objekt. 
    Om du vill meddela partnern att data har tagits emot kan du skicka tillbaka ett svar som innehåller dispositions meddelandet AS2 (MDN) i en HTTP-svars åtgärd.

12. Välj **Lägg till en åtgärd**för att lägga till **svars** åtgärden.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Om du vill filtrera alla åtgärder till den som du vill använda anger du ordet **svar** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Välj **svars** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. För att komma åt MDN från utdata från åtgärden **avkoda X12** , anger du fältet svars **text** med det här uttrycket:

    @base64ToString(brödtext (' Decode_AS2_message ')? [' OutgoingMdn']? [' Content '])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Spara ditt arbete.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Nu är du färdig med att konfigurera B2B-Logic-appen. I ett verkligt världs program kanske du vill lagra de avkodade X12-data i en affärsrelaterad app eller ett data lager. Om du vill ansluta dina egna LOB-appar och använda dessa API: er i din Logic app kan du lägga till ytterligare åtgärder eller skriva anpassade API: er.

## <a name="features-and-use-cases"></a>Funktioner och användnings fall

* Med AS2-och X12-åtgärderna kan du utbyta data mellan handels partner med hjälp av bransch standard protokoll i Logic Apps.
* Om du vill utbyta data med handels partner kan du använda AS2 och X12 med eller utan varandra.
* Med B2B-åtgärderna kan du enkelt skapa partner och avtal i ditt integrations konto och använda dem i en Logic app.
* När du utökar din Logic app med andra åtgärder kan du skicka och ta emot data mellan andra appar och tjänster som SalesForce.

## <a name="learn-more"></a>Läs mer
[Läs mer om Enterprise-integrationspaket](logic-apps-enterprise-integration-overview.md)
