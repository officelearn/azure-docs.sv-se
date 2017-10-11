---
title: "Skapa lösningar för B2B - Azure Logic Apps | Microsoft Docs"
description: "Ta emot data i logikappar med hjälp av B2B-funktioner i Enterprise-Integrationspaket"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Ta emot data i logikappar med B2B-funktioner i Enterprise-Integrationspaket

När du har skapat ett konto för integrering med partners och avtal du är redo att skapa ett företag att (B2B) arbetsflöde för din logikapp med den [Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Krav

Att använda AS2 och X12 åtgärder, du måste ha ett Enterprise Integration-konto. Läs [hur du skapar ett konto för Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Skapa en logikapp med B2B-kopplingar

Följ dessa steg om du vill skapa en B2B-logikapp som använder AS2 och X12 åtgärder för att ta emot data från en handelspartner:

1. Skapa en logikapp sedan [länka din app till ditt konto för integrering](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Lägg till en **begäran - när en HTTP-begäran tas emot** utlösaren till din logikapp.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Att lägga till den **avkoda AS2** åtgärd, Välj **lägga till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **as2** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Välj den **AS2 - avkoda AS2-meddelandet** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Lägg till den **brödtext** som du vill använda som indata. I det här exemplet väljer du innehållet i HTTP-begäran som utlöser logikappen. Eller ange ett uttryck som anger huvudena i den **HUVUDEN** fält:

    @triggerOutputs() [huvuden]

7. Lägg till de nödvändiga **huvuden** för AS2-, som du hittar i HTTP-huvuden för begäran. I det här exemplet väljer du till HTTP-huvuden som utlöser logikappen.

8. Nu ska du lägga till åtgärden för avkoda X12-meddelande. Välj **lägga till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **x12** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Välj den **X12-avkoda X12 meddelandet** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Nu måste du ange indata för den här åtgärden. Den här informationen är utdata från den föregående AS2-åtgärden.

    Det faktiska meddelandeinnehållet är i ett JSON-objekt och är base64-kodad, så du måste ange ett uttryck som indata. 
    Ange följande uttryck i den **X12 FLAT fil meddelandet till avkoda** inmatningsfältet:
    
    @base64ToString(body('Decode_AS2_message')? ['AS2Message']? ['Content'])

    Nu ska du lägga till stegen för att avkoda X12 data togs emot från handelspartner och utdata objekt i en JSON-objekt. 
    För att meddela partnern att data har tagits emot, kan du skicka tillbaka ett svar som innehåller Disposition Notification AS2 meddelande (MDN) i en åtgärd för HTTP-svar.

12. Att lägga till den **svar** åtgärd, Välj **lägga till en åtgärd**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Om du vill filtrera alla åtgärder för att det som du vill ange ordet **svar** i sökrutan.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Välj den **svar** åtgärd.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Åtkomst till MDN från utdata från den **avkoda X12 meddelandet** åtgärd, ange svaret **BRÖDTEXT** med det här uttrycket:

    @base64ToString(body('Decode_AS2_message')? ['OutgoingMdn']? ['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Spara ditt arbete.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Du är nu klar att konfigurera logikappen B2B. I ett verkligt program kanske du vill lagra den avkodade X12 data i en line-of-business (LOB) app eller data store. Du kan lägga till ytterligare åtgärder för att ansluta dina egna LOB-appar och använda dessa API: er i din logikapp, eller skriva anpassade API: er.

## <a name="features-and-use-cases"></a>Funktioner och användningsområden

* AS2 X12 avkoda och koda åtgärder kan du utbyta data mellan handelspartner med standardprotokollen i logikappar.
* Om du vill utbyta data med handelspartner kan du använda AS2 och X12 med eller utan varandra.
* B2B-åtgärder kan du enkelt skapa partners och avtal i kontot för integrering och använda dem i en logikapp.
* När du utökar din logikapp med andra åtgärder kan du skicka och ta emot data mellan andra appar och tjänster som SalesForce.

## <a name="learn-more"></a>Läs mer
[Mer information om Enterprise-Integrationspaket](logic-apps-enterprise-integration-overview.md)
