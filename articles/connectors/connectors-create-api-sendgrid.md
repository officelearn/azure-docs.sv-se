---
title: Ansluta till SendGrid från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som skickar e-postmeddelanden och hanterar e-postlistor i SendGrid med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 998020c5e39c8d50e8a14c74c43b7b435752f43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789315"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Skicka e-post och hantera e-postlistor i SendGrid med hjälp av Azure Logic Apps

Med Azure Logic Apps och SendGrid-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som skickar e-post och hanterar dina mottagarlistor, till exempel:

* Skicka e-post.
* Lägg till mottagare i listor.
* Få, lägga till och hantera globalt undertryckande.

Du kan använda SendGrid-åtgärder i logikapparna för att utföra dessa uppgifter. Du kan också låta andra åtgärder använda utdata från SendGrid-åtgärder. 

Den här kopplingen innehåller endast åtgärder, så för att starta logikappen använder du en separat utlösare, till exempel en **återkommande** utlösare. Om du till exempel regelbundet lägger till mottagare i dina listor kan du skicka e-post om mottagare och listor med office 365 Outlook-kopplingen eller Outlook.com-anslutningsappen.
Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [SendGrid-konto](https://www.sendgrid.com/) och en [SendGrid API-nyckel](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Din API-nyckel auktoriserar din logikapp för att skapa en anslutning och komma åt ditt SendGrid-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SendGrid-konto. Om du vill använda en SendGrid-åtgärd startar du **Recurrence** logikappen med en annan utlösare, till exempel upprepningsutlösaren.

## <a name="connect-to-sendgrid"></a>Ansluta till SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * Under det sista steget där du vill lägga till en åtgärd väljer du **Nytt steg**. 

     ELLER

   * Mellan de steg där du vill lägga till en åtgärd flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "sendgrid" som filter. Välj den åtgärd du vill använda under åtgärdslistan.

1. Ange ett namn på anslutningen. 

1. Ange din SendGrid API-nyckel och välj sedan **Skapa**.

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/sendgrid/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)