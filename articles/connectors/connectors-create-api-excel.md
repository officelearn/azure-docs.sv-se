---
title: Anslut till Excel Online – Azure Logic Apps | Microsoft Docs
description: 'Hantera data med Excel Online REST API: er och Azure Logic Apps'
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 917b9abd4a32b7951313c5555f4111dff990078c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230959"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Hantera Excel Online-data med Azure Logic Apps

Du kan skapa automatiserade uppgifter och arbetsflöden baserat på dina data i Excel Online för företag eller OneDrive med Azure Logic Apps och Excel Online connector. Den här anslutningstjänsten tillhandahåller åtgärder som hjälper dig att arbeta med dina data och hantera kalkylblad, till exempel: 

* Skapa nya kalkylblad och tabeller.
* Hämta och hantera kalkylblad, tabeller och rader.
* Lägg till enskild rader och kolumner.

Du kan sedan använda utdata från de här åtgärderna med åtgärder för andra tjänster. Om du använder en åtgärd som skapar kalkylblad varje vecka, kan du till exempel använda en annan åtgärd som skickar e-postbekräftelsen med hjälp av Office 365 Outlook-anslutningen.

Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> Den [Excel Online för företag](/connectors/excelonlinebusiness/) och [Excel Online för OneDrive](/connectors/excelonline/) anslutningsappar fungerar med Azure Logic Apps och skiljer sig från den [Excel-anslutningsapp för PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>. 

* En [Office 365-konto](https://www.office.com/) för ditt arbete eller personligt Microsoft-konto 

  Dina Excel-data kan finnas som en fil med kommaavgränsade värden (CSV)-fil i en mapp, till exempel i OneDrive. 
  Du kan också använda samma CSV-filen med den [fast-file connector](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt dina Excel Online-data. Den här anslutningstjänsten tillhandahåller endast åtgärder, så att starta logikappen, Välj en separat utlösare, till exempel, **upprepning** utlösaren.

## <a name="add-excel-action"></a>Lägg till Excel-åtgärd

1. I den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic App Designer, om inte redan är öppen.

1. Under utlösaren väljer **nytt steg**.

1. I sökrutan anger du ”excel” som filter. Välj vilken åtgärd du önska under åtgärder.

1. Om du uppmanas att logga in på Office 365-konto, väljer **logga in**. 

   Dina autentiseringsuppgifter för tillåta din logikapp för att skapa en anslutning till Excel Online och komma åt dina data.

1. Fortsätta att tillhandahålla nödvändig information för den valda åtgärden och skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel åtgärder och begränsningar som beskrivs av de kopplingar Swagger-filerna finns i dessa referenssidor för anslutningen:

* [Excel Online för företag](/connectors/excelonlinebusiness/) 
* [Excel Online för OneDrive](/connectors/excelonline/) 

## <a name="get-support"></a>Få support

* Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
