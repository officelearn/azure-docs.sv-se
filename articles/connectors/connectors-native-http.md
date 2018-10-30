---
title: Ansluta till alla HTTP-slutpunkt med Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som kommunicerar med valfri HTTP-slutpunkt med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 1c30f77c061ec25c88186caee3f60e65f3afb3de
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232876"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Anropa HTTP eller HTTPS-slutpunkter med Azure Logic Apps

Med Azure Logic Apps och Hypertext Transfer Protocol (HTTP)-anslutningstjänsten kan automatisera du arbetsflöden som kommunicerar med HTTP eller HTTPS-slutpunkten genom att skapa logikappar. Du kan till exempel övervaka tjänstslutpunkten för din webbplats. När en händelse inträffar vid den slutpunkten, till exempel din webbplats slutar fungera, händelsen utlöses logikappens arbetsflöde och kör de angivna åtgärderna. 

Du kan använda HTTP-utlösaren som det första steget i din arbetsflödet för att kontrollera eller *avsökning* en slutpunkt med jämna mellanrum. För varje kontroll utlösaren skickar ett anrop eller *begäran* till slutpunkten. Slutpunktens svaret avgör om din logikapp arbetsflödet körs. Utlösaren skickar med innehåll från svaret till åtgärder i din logikapp. 

Du kan använda HTTP-åtgärden som andra steg i arbetsflödet för att anropa slutpunkten när du vill. Slutpunktens svaret avgör hur din återstående arbetsflödesåtgärder köras.

Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* URL-Adressen för slutpunkten som mål som du vill anropa 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen som du vill att anropa mål-slutpunkt för att börja med HTTP-utlösare från [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Starta din logikapp med en utlösare för att använda HTTP-åtgärden.

## <a name="add-http-trigger"></a>Lägg till HTTP-utlösare

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen tom i Logic App Designer, om inte redan är öppna.

1. I sökrutan anger du ”http” som filter. Under listan över utlösare, väljer du den **HTTP** utlösaren. 

   ![Välj HTTP-utlösare](./media/connectors-native-http/select-http-trigger.png)

1. Ange den [HTTP-utlösare parametrar och värden](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) du vill ska ingå i anropet till mål-slutpunkten. Konfigurera upprepningen för hur ofta du vill att utlösaren för att kontrollera target endpont.

   ![Ange parametrar för HTTP-utlösare](./media/connectors-native-http/http-trigger-parameters.png)

   Mer information om HTTP-utlösare, parametrar och värden finns i [utlösare och åtgärd typer referens](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Fortsätt att skapa din logikapp arbetsflöde med åtgärder som körs när den utlöses.

## <a name="add-http-action"></a>Lägg till HTTP-åtgärd

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Under det sista steget där du vill lägga till HTTP-åtgärden, väljer **nytt steg**. 

   I det här exemplet börjar logikappen med HTTP-utlösaren som det första steget.

1. I sökrutan anger du ”http” som filter. Under åtgärdslistan väljer du den **HTTP** åtgärd.

   ![Välj HTTP-åtgärd](./media/connectors-native-http/select-http-action.png)

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange den [HTTP-åtgärd parametrar och värden](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) du vill ska ingå i anropet till mål-slutpunkten. 

   ![Ange HTTP-åtgärdsparametrar](./media/connectors-native-http/http-action-parameters.png)

1. När du är klar kan du kontrollera att du sparar din logikapp. Välj **Spara** i designerverktygsfältet. 

## <a name="authentication"></a>Autentisering

Om du vill ange autentisering, Välj **visa avancerade alternativ** inuti åtgärd eller utlösare. Mer information om tillgängliga autentiseringstyper för HTTP-utlösare och åtgärder finns i [utlösare och åtgärd typer referens](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
