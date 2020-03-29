---
title: Hantera data, kalkylblad och tabeller i Excel Online
description: Hantera data i kalkylblad och tabeller i Excel Online för företag eller Excel Online för OneDrive med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445872"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>Hantera Excel Online-data med Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och Excel Online for [Business-anslutningen](/connectors/excelonlinebusiness/) eller Excel Online [för OneDrive-anslutningsappen](/connectors/excelonline/) kan du skapa automatiserade uppgifter och arbetsflöden baserat på dina data i Excel Online för företag eller OneDrive. Den här kopplingen innehåller åtgärder som hjälper dig att arbeta med dina data och hantera kalkylblad, till exempel:

* Skapa nya kalkylblad och tabeller.
* Hämta och hantera kalkylblad, tabeller och rader.
* Lägg till enstaka rader och nyckelkolumner.

Du kan sedan använda utdata från dessa åtgärder med åtgärder för andra tjänster. Om du till exempel använder en åtgärd som skapar kalkylblad varje vecka kan du använda en annan åtgärd som skickar bekräftelsemeddelande via Office 365 Outlook-anslutningsappen.

Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excel Online för företag](/connectors/excelonlinebusiness/) och Excel Online för [OneDrive-kopplingar](/connectors/excelonline/) fungerar med Azure Logic Apps och skiljer sig från [Excel-anslutningen för PowerApps](/connectors/excel/).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* Ett [Office 365-konto](https://www.office.com/) för ditt arbetskonto eller ditt personliga Microsoft-konto

  Dina Excel-data kan finnas som en CSV-fil (comma-separated value) i en lagringsmapp, till exempel i OneDrive. 
  Du kan också använda samma CSV-fil med [flat-file-anslutningen](../logic-apps/logic-apps-enterprise-integration-flatfile.md).

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt dina Excel Online-data. Den här kopplingen innehåller endast åtgärder, så för att starta logikappen väljer du en separat utlösare, till exempel **utlösaren För upprepning.**

## <a name="add-excel-action"></a>Lägg till Excel-åtgärd

1. Öppna logikappen i Logic App Designer i [Azure-portalen,](https://portal.azure.com)om den inte redan är öppen.

1. Under utlösaren väljer du **Nytt steg**.

1. I sökrutan anger du "excel" som filter. Välj den åtgärd du vill använda under åtgärdslistan.

   > [!NOTE]
   > Logikappdesignern kan inte läsa in tabeller med 100 eller fler kolumner. Om möjligt kan du minska antalet kolumner i den valda tabellen så att designern kan läsa in tabellen.

1. Logga in på ditt Office 365-konto om du uppmanas att göra det.

   Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning till Excel Online och komma åt dina data.

1. Fortsätt att tillhandahålla nödvändig information för den valda åtgärden och skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen av kopplingens OpenAPI-filer (tidigare Swagger) finns på dessa anslutningsreferenssidor:

* [Excel Online för företag](/connectors/excelonlinebusiness/)
* [Excel Online för OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
