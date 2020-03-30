---
title: Anslut till Outlook.com
description: Automatisera uppgifter och arbetsflöden som hanterar e-post, kalendrar och kontakter i Outlook.com med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707194"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Hantera e-post, kalendrar och kontakter i Outlook.com med hjälp av Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Outlook.com-anslutningsappen](/connectors/outlook/)kan du skapa @outlook.com automatiserade uppgifter och arbetsflöden som hanterar ditt eller @hotmail.com ditt konto genom att skapa logikappar. Du kan till exempel automatisera dessa uppgifter:

* Skaffa, skicka och svara på e-post.
* Schemalägg möten i kalendern.
* Lägg till och redigera kontakter.

Du kan använda valfri utlösare för att starta arbetsflödet, till exempel när ett nytt e-postmeddelande anländer, när ett kalenderobjekt uppdateras eller när en händelse inträffar i en skillnadstjänst. Du kan använda åtgärder som svarar på utlösarhändelsen, till exempel skicka ett e-postmeddelande eller skapa en ny kalenderhändelse.

> [!NOTE]
> Om du vill automatisera uppgifter för @fabrikam.onmicrosoft.comett Microsoft-arbetskonto, till exempel, använder du [Office 365 Outlook-anslutningen](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Krav

* Ett [Outlook.com konto](https://outlook.live.com/owa/)

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Logikappen där du vill komma åt ditt Outlook.com konto. Om du vill starta arbetsflödet med en Outlook.com utlösare måste du ha en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Om du vill lägga till en Outlook.com åtgärd i arbetsflödet måste logikappen redan ha en utlösare.

## <a name="add-a-trigger"></a>Lägga till en utlösare

En [utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en händelse som startar arbetsflödet i logikappen. I det här exemplet använder logikappen en "avsökning" som söker efter ny e-post i ditt e-postkonto, baserat på det angivna intervallet och frekvensen.

1. Öppna din tomma logikapp i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Skriv "outlook.com" som filter i sökrutan. I det här exemplet väljer du **När ett nytt e-postmeddelande anländer**.

1. Om du uppmanas att logga in anger du Outlook.com autentiseringsuppgifter så att logikappen kan ansluta till ditt konto. Om anslutningen redan finns kan du ange informationen för utlösaregenskaperna:

1. I utlösaren ställer du in **frekvens-** och **intervallvärdena.**

   Om du till exempel vill att utlösaren ska avsöka var 15:e minut ställer du in **Frekvensen** på **Minut**och ställer in **intervallet** till **15**.

1. Välj **Spara**i designerverktygsfältet som sparar logikappen.

Om du vill svara på utlösaren lägger du till ytterligare en åtgärd. Du kan till exempel lägga till meddelandeåtgärden Twilio **Send,** som skickar ett sms när ett e-postmeddelande anländer.

## <a name="add-an-action"></a>Lägga till en åtgärd

En [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är en åtgärd som körs av arbetsflödet i logikappen. Logikappen skickar ett e-postmeddelande från ditt Outlook.com konto i det här exemplet. Du kan använda utdata från en annan utlösare för att fylla i åtgärden. Anta till exempel att logikappen använder SalesForce **När ett objekt skapas** utlösare. Du kan lägga till Outlook.com **Skicka en e-poståtgärd** och använda utdata från SalesForce-utlösaren i e-postmeddelandet.

1. Öppna logikappen i Logic App Designer i [Azure-portalen.](https://portal.azure.com)

1. Om du vill lägga till en åtgärd som det sista steget i arbetsflödet väljer du **Nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan dessa steg. Markera plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. Skriv "outlook.com" som filter i sökrutan. I det här exemplet väljer du **Skicka ett e-postmeddelande**. 

1. Om du uppmanas att logga in anger du Outlook.com autentiseringsuppgifter så att logikappen kan ansluta till ditt konto. Om anslutningen redan finns kan du ange informationen för åtgärdsegenskaperna om anslutningen redan finns.

1. Välj **Spara**i designerverktygsfältet som sparar logikappen.

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](/connectors/outlook/). 

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)
