---
title: Ansluta till Salesforce från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som övervakar, skapar och hanterar Salesforce-poster och jobb med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789298"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera Salesforce-resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Salesforce-kopplingen kan du skapa automatiserade uppgifter och arbetsflöden för dina Salesforce-resurser, till exempel poster, jobb och objekt, till exempel:

* Övervaka när poster skapas eller ändras. 
* Skapa, hämta och hantera jobb och poster, inklusive infoga, uppdatera och ta bort åtgärder.

Du kan använda Salesforce-utlösare som får svar från Salesforce och gör utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i logikapparna för att utföra uppgifter med Salesforce-resurser. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Ett [Salesforce-konto](https://salesforce.com/)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Salesforce-konto. Om du vill börja med en Salesforce-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Salesforce-åtgärd startar du logikappen med en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-salesforce"></a>Ansluta till Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * För tomma logikappar anger du "salesforce" som filter i sökrutan. 
   Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar väljer du **Nytt steg**under det steg där du vill lägga till en åtgärd . Ange "salesforce" som filter i sökrutan. Välj den åtgärd du vill använda under åtgärdslistan.

1. Om du uppmanas att logga in på Salesforce loggar du in nu och ger åtkomst.

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning till Salesforce och komma åt dina data.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/salesforce/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)