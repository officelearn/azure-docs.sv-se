---
title: Ansluta till RSS-feeds från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar RSS-feeds med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789349"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Hantera RSS-feeds med hjälp av Azure Logic Apps

Med Azure Logic Apps och RSS-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden för alla RSS-feedar, till exempel:

* Övervaka när RSS-feedobjekt publiceras.
* Lista alla RSS-feedobjekt.

RSS (Rich Site Summary), även kallad Really Simple Syndication, är ett populärt format för webbsyndikering och används för publicering av ofta uppdaterat innehåll, till exempel blogginlägg och nyhetsrubriker. Många innehållsutgivare tillhandahåller en RSS-feed så att användarna kan prenumerera på det innehållet. 

Du kan använda en RSS-utlösare som hämtar svar från en RSS-feed och gör utdata tillgängliga för andra åtgärder. Du kan använda en RSS-åtgärd i logikapparna för att utföra en uppgift med RSS-feeden. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* URL:en för ett RSS-flöde

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt en RSS-feed. Om du vill börja med en RSS-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en RSS-åtgärd startar du logikappen med en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-an-rss-feed"></a>Ansluta till ett RSS-flöde

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * För tomma logikappar anger du "rss" som filter i sökrutan. Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar väljer du **Nytt steg**under det steg där du vill lägga till en åtgärd . I sökrutan skriver du "rss" som filter. Välj den åtgärd du vill använda under åtgärdslistan.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/rss/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)