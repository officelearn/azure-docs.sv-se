---
title: Ansluta till Project Online från Azure Logic Apps
description: Automatisera arbetsflöden som övervakar, skapar och hanterar Project Online-projekt, uppgifter och resurser med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789332"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Hantera Projekt online-projekt, aktiviteter och resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Project Online-anslutningsappen kan du skapa automatiserade uppgifter och arbetsflöden för dina projekt, aktiviteter och resurser i Project Online via Office 365. Dina arbetsflöden kan utföra dessa åtgärder och andra, till exempel:

* Övervaka när nya projekt, aktiviteter eller resurser skapas. Eller övervaka när nya projekt publiceras.
* Skapa nya projekt, aktiviteter eller resurser.
* Lista befintliga projekt eller uppgifter.
* Checka ut, checka in eller publicera projekt.

Project Online hjälper dig att planera, prioritera och hantera projekt och projektportföljinvesteringar från nästan var som helst på nästan vilken enhet som helst genom att tillhandahålla kraftfulla projekthanteringsfunktioner. Du kan använda Project Online-utlösare som får svar från Project Online och gör utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i logikapparna för att utföra olika uppgifter i Project Online. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Project Online, tillgängligt via ett [Office 365-konto](https://www.office.com/), 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt dina Project Online-data. Om du vill börja med en Project Online-utlösare [skapar du en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda Project Online-åtgärder startar du logikappen med en annan utlösare, till exempel **utlösaren För återkommande.**

## <a name="connect-to-project-online"></a>Ansluta till Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Välj en bana: 

   * Om du vill ha tomma logikappar anger du "Project Online" som filter i sökrutan. 
   Markera den utlösare du vill använda under listan utlösare. 

     ELLER

   * För befintliga logikappar väljer du **Nytt steg**under det steg där du vill lägga till en åtgärd . Skriv "Project Online" som filter i sökrutan. Välj den åtgärd du vill använda under åtgärdslistan.

1. Om du uppmanas att logga in på Project Online loggar du in nu.

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning till Project Online och komma åt dina data.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar, som beskrivs av kopplingens OpenAPI-beskrivning (tidigare Swagger) finns i kopplingens [referenssida](/connectors/projectonline/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)