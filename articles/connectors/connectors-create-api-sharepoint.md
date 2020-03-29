---
title: Ansluta till SharePoint från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar resurser i SharePoint Online eller SharePoint Server lokalt med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: bb82ef2d6fb83c2e1b0fa81aa9504c9bb7d8234b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789264"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Övervaka och hantera SharePoint-resurser med Azure Logic Apps

Med Azure Logic Apps och SharePoint-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden som övervakar och hanterar resurser, till exempel filer, mappar, listor, objekt, personer och så vidare, i SharePoint Online eller i SharePoint Server lokalt, till exempel:

* Övervaka när filer eller objekt skapas, ändras eller tas bort.
* Skapa, hämta, uppdatera eller ta bort objekt.
* Lägg till, hämta eller ta bort bifogade filer. Hämta innehållet från bifogade filer.
* Skapa, kopiera, uppdatera eller ta bort filer. 
* Uppdatera filegenskaper. Hämta innehåll, metadata eller egenskaper för en fil.
* Lista eller extrahera mappar.
* Hämta listor eller listvyer.
* Ange status för godkännande av innehåll.
* Lös personer.
* Skicka HTTP-begäranden till SharePoint.
* Hämta entitetsvärden.

Du kan använda utlösare som får svar från SharePoint och gör utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i logikapparna för att utföra uppgifter i SharePoint. Du kan också låta andra åtgärder använda utdata från SharePoint-åtgärder. Om du till exempel regelbundet hämtar filer från SharePoint kan du skicka meddelanden till teamet med slackkopplingen.
Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Din SharePoint-webbplatsadress och användaruppgifter

  Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt SharePoint-konto. 

* Innan du kan ansluta logikappar till lokala system som SharePoint Server måste du [installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange att gatewayinstallationen ska användas när du skapar SharePoint Server-anslutningen för logikappen.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SharePoint-konto. Om du vill börja med en SharePoint-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en SharePoint-åtgärd startar du logikappen med en utlösare, till exempel en Salesforce-utlösare, om du har ett Salesforce-konto.

  Du kan till exempel starta logikappen med Salesforce-utlösaren **När en post skapas.** 
  Den här utlösaren utlöses varje gång en ny post, till exempel ett lead, skapas i Salesforce. 
  Du kan sedan följa den här utlösaren med **filåtgärden Skapa** SharePoint. På så sätt, när den nya posten skapas, skapar logikappen en fil i SharePoint med information om den nya posten.

## <a name="connect-to-sharepoint"></a>Anslut till SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. För tomma logikappar anger du "sharepoint" som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare. 

   ELLER

   För befintliga logikappar väljer du **Nytt steg**under det sista steget där du vill lägga till en SharePoint-åtgärd . 
   Ange "sharepoint" som filter i sökrutan. 
   Välj den åtgärd du vill använda under åtgärdslistan.

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. När du uppmanas att logga in anger du nödvändig anslutningsinformation. Om du använder SharePoint Server kontrollerar du att du väljer **Anslut via lokal datagateway**. När du är klar väljer du **Skapa**.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/sharepoint/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
