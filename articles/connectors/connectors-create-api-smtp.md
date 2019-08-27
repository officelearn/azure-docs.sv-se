---
title: Ansluta till SMTP från Azure Logic Apps | Microsoft Docs
description: Automatisera aktiviteter och arbets flöden som skickar e-post via ditt SMTP-konto (Simple Mail Transfer Protocol) med Azure Logic Apps
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
ms.openlocfilehash: 1649f197d4dbd88e2b485ab32f254a2d09696a84
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050738"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Skicka e-post från ditt SMTP-konto med Azure Logic Apps

Med Azure Logic Apps och Simple Mail Transfer Protocol-anslutningen (SMTP) kan du skapa automatiserade uppgifter och arbets flöden som skickar e-post från ditt SMTP-konto. Du kan också använda andra åtgärder för att använda utdata från SMTP-åtgärder. När din SMTP till exempel skickar ett e-postmeddelande kan du meddela ditt team i slack med slack-kopplingen. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ditt SMTP-konto och användarautentiseringsuppgifter

  Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning och komma åt ditt SMTP-konto.

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du vill komma åt ditt SMTP-konto. Om du vill använda en SMTP-åtgärd startar du din Logic app med en utlösare, till exempel en Salesforce-utlösare, om du har ett Salesforce-konto.

  Du kan till exempel starta din Logi Kap par med **när en post skapas** i Salesforce-utlösare. 
  Den här utlösaren utlöses varje gång en ny post, till exempel ett lead, skapas i Salesforce. 
  Du kan sedan följa den här utlösaren med åtgärden SMTP **skicka e-post** . På så sätt skickar din Logic app ett e-postmeddelande från ditt SMTP-konto om den nya posten när den nya posten skapas.

## <a name="connect-to-smtp"></a>Anslut till SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Under det sista steget där du vill lägga till en SMTP-åtgärd väljer du **nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet ( **+** ) som visas och välj sedan **Lägg till en åtgärd**.

1. I rutan Sök anger du "SMTP" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

1. När du uppmanas att ange den här anslutnings informationen:

   | Egenskap | Obligatorisk | Beskrivning |
   |----------|----------|-------------|
   | **Anslutningsnamn** | Ja | Ett namn på anslutningen till SMTP-servern | 
   | **SMTP-serveradress** | Ja | Adressen till SMTP-servern | 
   | **Användar namn** | Ja | Ditt användar namn för ditt SMTP-konto | 
   | **Lösenord** | Ja | Ditt lösen ord för ditt SMTP-konto | 
   | **SMTP-serverport** | Nej | En speciell port på SMTP-servern som du vill använda | 
   | **Vill du aktivera SSL?** | Nej | Aktivera eller inaktivera SSL-kryptering. | 
   |||| 

1. Ange nödvändig information för den valda åtgärden. 

1. Spara din Logic app eller Fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/smtpconnector/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)