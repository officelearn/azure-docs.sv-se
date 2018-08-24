---
title: Anslut till Box – Azure Logic Apps | Microsoft Docs
description: 'Skapa och hantera filer med rutan REST API: er och Azure Logic Apps'
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: b5c8c18c6d02710646560f29d4bc7b5784f730a2
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746885"
---
# <a name="create-and-manage-files-in-box-with-azure-logic-apps"></a>Skapa och hantera filer i Box med Azure Logic Apps

Den här artikeln visar hur du kan skapa och hantera dina filer i Box från inuti en logikapp med Box-anslutningsprogrammet. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer och andra åtgärder, till exempel:

* Skapa ditt flöde för företag som baseras på data som du får från Box. 

* Utlösa automatiserade uppgifter och arbetsflöde när en fil skapas eller uppdateras.

* Köra åtgärder som kopierar en fil, tar bort en fil och mycket mer. 

  När de här åtgärderna får ett svar kan se de utdata tillgänglig för andra åtgärder. 
  Till exempel när en fil ändras i Box, kan du skicka filen via e-post med hjälp av Office 365.

## <a name="prerequisites"></a>Förutsättningar

* En [rutan konto](https://www.box.com/home)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* Logikappen där du vill komma åt ditt Box-konto. Om du vill börja din logikapp med en Box-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, enligt beskrivningen av kopplingens Swagger-fil, finns i den [anslutningsappens-referenssida](/connectors/box/). 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)