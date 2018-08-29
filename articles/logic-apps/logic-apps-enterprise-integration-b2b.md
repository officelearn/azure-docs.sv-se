---
title: Skapa en B2B enterprise-integrering – Azure Logic Apps | Microsoft Docs
description: Ta emot B2B-data i Azure Logic Apps med Enterprise-Integrationspaket
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.date: 07/08/2016
ms.openlocfilehash: ad7a29f4a554d599b17576921542b1ac6e403911
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127772"
---
# <a name="receive-b2b-data-with-azure-logic-apps-and-enterprise-integration-pack"></a>Ta emot B2B-data med Azure Logic Apps och Enterprise-Integrationspaket

När du har skapat ett konto för integrering med partners och avtal du är redo att skapa ett arbetsflöde för företag (B2B) för din logikapp med den [Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Förutsättningar

Du använder AS2 och X12 åtgärder, du måste ha ett Enterprise-Integrationskonto. Lär dig [så här skapar du en Enterprise-Integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Skapa en logikapp med B2B-kopplingar

Följ stegen nedan för att skapa en B2B-logikapp som använder AS2 och X12 åtgärder tar emot data från en handelspartner:

1. Skapa en logikapp sedan [länka din app till ditt integrationskonto](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Lägg till en **begäran – när en HTTP-begäran tas emot** utlösare i logikappen.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Att lägga till den **avkoda AS2** väljer **Lägg till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **as2** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Välj den **AS2 - avkoda AS2-meddelandet** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Lägg till den **brödtext** som du vill använda som indata. I det här exemplet väljer du brödtexten i HTTP-begäran som utlöser logikappen. Eller ange ett uttryck som indata huvudena i den **RUBRIKER** fält:

    @triggerOutputs() [”headers”]

7. Lägg till de nödvändiga **rubriker** för AS2-, som du hittar i HTTP-begärans sidhuvud. I det här exemplet väljer du sidhuvuden för HTTP-begäran som utlöser logikappen.

8. Nu ska du lägga till åtgärden för avkodningen X12-meddelande. Välj **Lägg till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **x12** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Välj den **X12-avkoda X12 meddelande** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Du måste nu ange indata till den här åtgärden. Indata för det här är utdata från föregående AS2-åtgärd.

    Faktiska meddelandeinnehåll är i ett JSON-objekt och är base64-kodat, så du måste ange ett uttryck som indata. 
    Ange följande uttryck i den **X12 för platt fil meddelandet till AVKODNINGEN** inmatningsfält:
    
    @base64ToString(body('Decode_AS2_message')? ['AS2Message']? ['Content'])

    Nu ska du lägga till stegen för att avkoda X12 data togs emot från handelspartner och utdata objekt i ett JSON-objekt. 
    För att meddela partnern att data togs emot, kan du skicka tillbaka ett svar som innehåller Disposition meddelande AS2 meddelande (MDN) i en åtgärd för HTTP-svar.

12. Att lägga till den **svar** åtgärd, Välj **Lägg till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **svar** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Välj den **svar** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Åtkomst till MDN från utdata från den **avkodningen X12 meddelande** , anger svaret **BRÖDTEXT** fältet med det här uttrycket:

    @base64ToString(body('Decode_AS2_message')? ['OutgoingMdn']? ['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Spara ditt arbete.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Du är nu klar att konfigurera logikappen B2B. I en verklig tillämpning kanske du vill lagra den avkodade X12 data i en line-of-business (LOB) appar eller data store. Du kan lägga till ytterligare åtgärder för att ansluta dina egna LOB-appar och använda dessa API: er i din logikapp, eller skriva anpassade API: er.

## <a name="features-and-use-cases"></a>Funktioner och användningsfall

* AS2 och X12 avkoda och koda åtgärder kan du byta mellan handelspartner med hjälp av protokoll av branschstandardtyp i logic apps.
* För att utbyta data med handelspartner, kan du använda AS2 och X12 med eller utan varandra.
* B2B-åtgärder för att skapa partners och avtal enkelt i ditt integrationskonto och använda dem i en logikapp.
* När du utökar din logikapp med andra åtgärder kan du skicka och ta emot data mellan andra appar och tjänster som SalesForce.

## <a name="learn-more"></a>Läs mer
[Mer information om Enterprise-Integrationspaketet](logic-apps-enterprise-integration-overview.md)
