---
title: Ansluta till SMTP från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som skickar e-post via SMTP (Simple Mail Transfer Protocol)-konto med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230513"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Skicka e-postmeddelande från din SMTP-konto med Azure Logic Apps

Med Azure Logic Apps och Simple Mail Transfer Protocol (SMTP)-anslutningstjänsten kan skapa du automatiserade uppgifter och arbetsflöden som skickar e-post från din SMTP-kontot. Du kan också ha andra åtgärder som använder utdata från SMTP-åtgärder. När din SMTP skickar ett e-postmeddelande, kan du meddela din grupp i Slack med Slack-anslutningsprogrammet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Dina autentiseringsuppgifter för SMTP-kontot och användare

  Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och åtkomst till SMTP-kontot.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt din SMTP-kontot. Starta din logikapp med en utlösare, till exempel en Salesforce-utlösare för att använda en SMTP-åtgärden, om du har en Salesforce-konto.

  Du kan till exempel börja din logikapp med den **när en post skapas** Salesforce-utlösare. 
  Den här utlösaren utlöses varje gång som en ny post, till exempel ett lead skapas i Salesforce. 
  Sedan kan du följa den här utlösaren med SMTP **skicka e-post** åtgärd. På så sätt kan när den nya posten skapas, skickar logikappen ett e-postmeddelande från din SMTP-kontot om den nya posten.

## <a name="connect-to-smtp"></a>Ansluta till SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Under det sista steget där du vill lägga till en SMTP-åtgärden, väljer **nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”smtp” som filter. Välj vilken åtgärd du önska under åtgärder.

1. När du uppmanas, anger du den här anslutningsinformationen:

   | Egenskap  | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Anslutningsnamn** | Ja | Ett namn för anslutningen till din SMTP-server | 
   | **SMTP-serveradress** | Ja | Adress för din SMTP-server | 
   | **Användarnamn** | Ja | Ditt användarnamn för SMTP-kontot | 
   | **Lösenord** | Ja | Lösenordet för SMTP-kontot | 
   | **SMTP-serverport** | Nej | En viss port på SMTP-servern som du vill använda | 
   | **Aktivera SSL?** | Nej | Aktivera eller inaktivera SSL-kryptering. | 
   |||| 

1. Ange informationen som krävs för den valda åtgärden. 

1. Spara din logikapp eller Fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/smtpconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)