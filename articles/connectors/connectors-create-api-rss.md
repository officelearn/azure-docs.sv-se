---
title: Ansluta till RSS-flöden från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar RSS-flöden med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233063"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Hantera RSS-flöden med hjälp av Azure Logic Apps

Med Azure Logic Apps och RSS-anslutningsappen kan du kan skapa automatiserade uppgifter och arbetsflöden för alla RSS-feed, till exempel:

* Övervaka när RSS-flödesobjekt publiceras.
* Lista alla RSS-flödesobjekt.

RSS (Rich Site sammanfattning), även kallat verkligen enkelt syndikering är ett vanligt format för web-syndikering och används för att publicera innehåll som uppdateras ofta, till exempel blogginlägg och nyhetsrubriker. Många innehållsutgivare har ett RSS-flöde så att användare kan prenumerera på innehållet. 

Du kan använda en RSS-utlösare som får svar från en RSS-feed och gör utdata som är tillgängliga för andra åtgärder. Du kan använda en RSS-åtgärd i dina logic apps för att utföra en uppgift med RSS-flödet. Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* URL-Adressen för en RSS-feed

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill öppna en RSS-feed. Du kommer igång med en RSS-utlösare [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill använda en RSS-åtgärd, starta din logikapp med en annan utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-an-rss-feed"></a>Ansluta till en RSS-feed

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Välj en sökväg: 

   * För tom logic apps i sökrutan anger du ”rss” som filter. Välj utlösaren som du vill under listan över utlösare. 

     ELLER

   * För befintliga logikappar under steget där du vill lägga till en åtgärd, Välj **nytt steg**. I sökrutan skriver du "rss" som filter. Välj vilken åtgärd du önska under åtgärder.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/rss/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)