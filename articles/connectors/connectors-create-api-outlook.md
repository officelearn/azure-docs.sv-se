---
title: Ansluta till Outlook.com – Azure Logic Apps | Microsoft Docs
description: 'Hantera e-post, kalendrar och kontakter med Outlook.com REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50228763"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Hantera e-post, kalendrar och kontakter i Outlook.com med Azure Logic Apps

Den här artikeln visar hur du kan skapa och hantera din Outlook.com-konto i en logikapp med Box-anslutningsprogrammet. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för ditt Outlook.com-konto, till exempel:

* Skicka e-post. 
* Schemalägga möten.
* Lägga till kontakter. 

Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* En [Outlook.com-konto](https://outlook.live.com/owa/)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Logikappen där du vill komma åt ditt Outlook.com-konto. Om du vill starta logikappen med en Outlook-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Ansluta till Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, enligt beskrivningen av kopplingens Swagger-fil, finns i den [anslutningsappens-referenssida](/connectors/outlook/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)