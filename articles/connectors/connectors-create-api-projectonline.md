---
title: Ansluta till Project Online från Azure Logic Apps
description: Automatisera arbets flöden som övervakar, skapar och hanterar projekt online-projekt,-uppgifter och-resurser med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a3e90fa3e3f57c1575a7ab09f9ce6941c13adcd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83834874"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Hantera Project Online-projekt,-uppgifter och-resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Project Online Connector kan du skapa automatiserade uppgifter och arbets flöden för dina projekt, aktiviteter och resurser i Project Online via Office 365. Dina arbets flöden kan utföra dessa åtgärder och andra, till exempel:

* Övervaka när nya projekt, uppgifter eller resurser skapas. Eller övervaka när nya projekt publiceras.
* Skapa nya projekt, uppgifter eller resurser.
* Visa en lista över befintliga projekt eller uppgifter.
* Kolla in, checka in eller publicera projekt.

Project Online hjälper dig att planera, prioritera och hantera projekt och projekt portfölj investeringar från nästan var som helst på nästan vilken enhet som helst genom att tillhandahålla kraftfulla funktioner för projekt hantering. Du kan använda Project Online-utlösare som får svar från Project Online och göra utdata tillgängliga för andra åtgärder. Du kan använda åtgärder i Logi Kap par för att utföra olika uppgifter i Project Online. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Project Online, som är tillgängligt via ett [Office 365-konto](https://www.office.com/), 

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic-app där du vill komma åt dina Project Online-data. Börja med en Project Online-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda Project Online-åtgärder startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-project-online"></a>Ansluta till Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "Project Online" som filter i rutan Sök. 
   Välj den utlösare som du vill använda under listan utlösare. 

     \- eller -

   * För befintliga Logi Kap par väljer du **nytt steg**under steget där du vill lägga till en åtgärd. Skriv "Project Online" som filter i rutan Sök. Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Om du uppmanas att logga in på Project Online loggar du in nu.

   Dina autentiseringsuppgifter ger din Logic-app behörighet att skapa en anslutning till Project Online och komma åt dina data.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/projectonline/).

## <a name="get-support"></a>Få support

* För frågor, besök [sidan Microsoft Q&en fråga för Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)