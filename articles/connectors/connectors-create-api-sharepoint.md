---
title: Anslut till SharePoint från Azure Logic Apps | Microsoft Docs
description: Automatisera uppgifter och arbetsflöden som övervakar och hanterar resurser i SharePoint Online eller SharePoint Server lokalt med hjälp av Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60450751"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Övervaka och hantera SharePoint-resurser med Azure Logic Apps

Med Azure Logic Apps och SharePoint-anslutningsappen kan skapa du automatiserade uppgifter och arbetsflöden som övervakar och hanterar resurser, till exempel filer, mappar, listor, objekt, personer, och så vidare i SharePoint Online eller SharePoint Server på plats, till exempel:

* Övervaka när filer eller objekt som skapas, ändras eller tas bort.
* Skapa, hämta, uppdatera eller ta bort objekt.
* Lägga till, hämta eller ta bort bifogade filer. Hämta innehållet från bifogade filer.
* Skapa, kopiera, uppdatera eller ta bort filer. 
* Uppdatera filegenskaperna. Hämta innehåll, metadata och egenskaper för en fil.
* Lista eller extrahera mappar.
* Hämta listor eller listvyer.
* Ange status för innehållsgodkännande.
* Lös personer.
* Skicka HTTP-begäranden till SharePoint.
* Hämta entitetsvärden.

Du kan använda utlösare som få svar från SharePoint och göra utdata som är tillgängliga för andra åtgärder. Du kan använda åtgärder i dina logic apps för att utföra uppgifter i SharePoint. Du kan också ha andra åtgärder som använder utdata från SharePoint-åtgärder. Exempel: Om du regelbundet hämta filer från SharePoint, kan du skicka meddelanden till ditt team för med Slack-anslutningen.
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Din plats adressen och användarnamnet autentiseringsuppgifter för SharePoint

  Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning och komma åt ditt konto för SharePoint. 

* Innan du kan ansluta logikappar till lokala system, till exempel SharePoint Server, måste du [installera och konfigurera en lokal datagateway](../logic-apps/logic-apps-gateway-install.md). På så sätt kan du ange om du vill använda din gatewayinstallationen när du skapar SharePoint Server-anslutning för din logikapp.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt konto för SharePoint. Du kommer igång med en SharePoint-utlösare, [skapa en tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Starta din logikapp med en utlösare, till exempel en Salesforce-utlösare för att använda en SharePoint-åtgärd, om du har en Salesforce-konto.

  Du kan till exempel börja din logikapp med den **när en post skapas** Salesforce-utlösare. 
  Den här utlösaren utlöses varje gång som en ny post, till exempel ett lead skapas i Salesforce. 
  Sedan kan du följa den här utlösaren med SharePoint **skapa fil** åtgärd. På så sätt kan när den nya posten skapas, logikappen skapas en fil i SharePoint med information om den nya posten.

## <a name="connect-to-sharepoint"></a>Anslut till SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. För tom logic apps i sökrutan anger du ”sharepoint” som filter. Välj utlösaren som du vill under listan över utlösare. 

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en SharePoint-åtgärd, Välj **nytt steg**. 
   I sökrutan anger du ”sharepoint” som filter. 
   Välj vilken åtgärd du önska under åtgärder.

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (**+**) som visas och välj sedan **Lägg till en åtgärd**.

1. Ange anslutningsinformationen som krävs när du uppmanas att logga in. Om du använder SharePoint Server, kontrollera att du väljer **Anslut via lokal datagateway**. När du är klar väljer du **Skapa**.

1. Ange informationen som krävs för din valda utlösare eller åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](/connectors/sharepoint/).

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
