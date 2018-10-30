---
title: Anslut till Project Online från Azure Logic Apps | Microsoft Docs
description: Automatisera arbetsflöden som övervakar, skapa och hantera Project Online-projekt, uppgifter och resurser med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230377"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Hantera Project Online-projekt, uppgifter och resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Project Online connector kan du skapa automatiserade uppgifter och arbetsflöden för ditt projekt, uppgifter och resurser i Project Online via Office 365. Dina arbetsflöden kan utföra dessa åtgärder och andra, till exempel:

* Övervakare för när nya projekt, aktiviteter eller resurser skapas. Du kan också övervaka nya projekt som publiceras.
* Skapa nya projekt, aktiviteter eller resurser.
* Lista befintliga projekt eller uppgifter.
* Checka ut, checka in eller publicera projekt.

Project Online hjälper dig att planera, prioritera och hantera projekt och projektportföljer från nästan var som helst på nästan vilken enhet genom att tillhandahålla kraftfulla funktioner. Du kan använda Project Online-utlösare som få svar från Project Online och göra utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder i dina logic apps för att utföra olika uppgifter i Project Online. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Project Online, tillgängligt via en [Office 365-konto](https://www.office.com/), 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill få åtkomst till din Project Online-data. Du kommer igång med en Project Online-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda Project Online åtgärder, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-project-online"></a>Anslut till Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”Project Online” som filter. 
   Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logikappar under steget där du vill lägga till en åtgärd, Välj **nytt steg**. I sökrutan anger du ”Project Online” som filter. Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in på Project Online, logga in nu.

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning till Project Online och komma åt dina data.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/projectonline/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)