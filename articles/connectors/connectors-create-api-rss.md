---
title: Ansluta till RSS-flöden från Azure Logic Apps
description: Automatisera uppgifter och arbets flöden som övervakar och hanterar RSS-flöden med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 3f1e092c2ff325cdcbc32c617af316d6fbe6dd74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789349"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Hantera RSS-flöden med Azure Logic Apps

Med Azure Logic Apps och RSS-anslutningen kan du skapa automatiserade uppgifter och arbets flöden för RSS-flöden, till exempel:

* Övervaka när RSS-feeds-objekt publiceras.
* Lista alla RSS-feeds-objekt.

RSS (utförlig sammanfattning av webbplats), även kallat faktiskt enkelt syndikering, är ett populärt format för webb syndikering och används för att publicera ofta uppdaterat innehåll, till exempel blogg inlägg och nyhets rubriker. Många innehålls utgivare tillhandahåller en RSS-feed så att användarna kan prenumerera på innehållet. 

Du kan använda en RSS-utlösare som hämtar svar från en RSS-feed och gör utdata tillgängliga för andra åtgärder. Du kan använda en RSS-åtgärd i dina Logi Kap par för att utföra en uppgift med RSS-flödet. Om du är nybörjare på Logi Kap par kan du läsa om [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* URL för en RSS-feed

* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill komma åt en RSS-feed. Börja med en RSS-utlösare genom att [skapa en tom Logic-app](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en RSS-åtgärd startar du din Logic-app med en annan utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-an-rss-feed"></a>Ansluta till en RSS-feed

1. Logga in på [Azure Portal](https://portal.azure.com)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. Välj en sökväg: 

   * För tomma Logi Kap par anger du "RSS" som filter i rutan Sök. Välj den utlösare som du vill använda under listan utlösare. 

     ELLER

   * För befintliga Logi Kap par väljer du **nytt steg**under steget där du vill lägga till en åtgärd. I sökrutan skriver du "rss" som filter. Under listan åtgärder väljer du den åtgärd som du vill använda.

1. Ange nödvändig information för den valda utlösaren eller åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](/connectors/rss/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)