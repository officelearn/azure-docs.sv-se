---
title: Ansluta till SendGrid från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som skickar e-post och hantera distributionslistor SendGrid med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 7eecd3908883b195b52755d03e70872afe9180bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105742"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Skicka e-post och hantera distributionslistor SendGrid med Azure Logic Apps

Du kan skapa automatiserade uppgifter och arbetsflöden som skickar e-post och hantera mottagarlistor, till exempel med Azure Logic Apps och SendGrid-anslutningen:

* Skicka e-post.
* Lägg till mottagare i listor.
* Hämta, lägga till och hantera globala undertryckandet.

Du kan använda SendGrid-åtgärder i dina logic apps för att utföra dessa uppgifter. Du kan också ha andra åtgärder som använder utdata från SendGrid-åtgärder. 

Den här anslutningstjänsten tillhandahåller endast åtgärder, så att starta logikappen, använda en separat utlösare, som en **upprepning** utlösaren. Om du regelbundet lägger till mottagare i dina listor, kan du skicka e-postmeddelandets mottagare och listor med hjälp av anslutningsappen Office 365 Outlook eller Outlook.com-anslutning.
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* En [SendGrid-konto](https://www.sendgrid.com/) och en [SendGrid API-nyckel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Din API-nyckel auktoriserar din logikapp för att skapa en anslutning och komma åt ditt SendGrid-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SendGrid-konto. Om du vill använda en SendGrid-åtgärd, börja din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-sendgrid"></a>Ansluta till SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * Under det sista steget där du vill lägga till en åtgärd, väljer **nytt steg**. 

     ELLER

   * Mellan stegen där du vill lägga till en åtgärd, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du ”sendgrid” som filter. Välj vilken åtgärd du önska under åtgärder.

1. Ange ett namn på anslutningen. 

1. Ange din SendGrid API-nyckel och välj **skapa**.

1. Ange informationen som krävs för din valda åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sendgrid/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)