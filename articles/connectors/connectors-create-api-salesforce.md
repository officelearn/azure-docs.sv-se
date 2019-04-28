---
title: Ansluta till Salesforce från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar, skapa och hantera Salesforce-poster och jobb med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106023"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Övervaka, skapa och hantera Salesforce-resurser med hjälp av Azure Logic Apps

Med Azure Logic Apps och Salesforce-anslutningen kan kan du skapa automatiserade uppgifter och arbetsflöden för dina Salesforce-resurser, till exempel poster, jobb och objekt, till exempel:

* Övervakare för när poster skapas eller ändras. 
* Skapa, hämta, och hantera jobb och -poster, inklusive insert-, update och ta bort åtgärder.

Du kan använda Salesforce-utlösare som få svar från Salesforce och se utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder i dina logic apps för att utföra åtgärder med Salesforce-resurser. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* En [Salesforce-konto](https://salesforce.com/)

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt Salesforce-konto. Du kommer igång med en Salesforce-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en Salesforce-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-salesforce"></a>Anslut till Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”salesforce” som filter. 
   Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logikappar under steget där du vill lägga till en åtgärd, Välj **nytt steg**. I sökrutan anger du ”salesforce” som filter. Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in till Salesforce, logga in nu och ge åtkomst.

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning till Salesforce och komma åt dina data.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/salesforce/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)