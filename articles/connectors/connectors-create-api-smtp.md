---
title: Ansluta till SMTP från Azure Logic Apps
description: Automatisera uppgifter och arbetsflöden som skickar e-post via ditt SMTP-konto (Simple Mail Transfer Protocol) med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 60acd128495176cd0a90418c61edf53bdcd88e5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77647584"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Skicka e-post från ditt SMTP-konto med Azure Logic Apps

Med Azure Logic Apps och SMTP-anslutningsappen (Simple Mail Transfer Protocol) kan du skapa automatiserade uppgifter och arbetsflöden som skickar e-post från ditt SMTP-konto. Du kan också låta andra åtgärder använda utdata från SMTP-åtgärder. När SMTP till exempel har skickat ett e-postmeddelande kan du meddela ditt team i Slack med Slack-kopplingen. Om du inte har tidigare i logikappar läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Dina SMTP-konto och användaruppgifter

  Dina autentiseringsuppgifter ger logikappen behörighet att skapa en anslutning och komma åt ditt SMTP-konto.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt ditt SMTP-konto. Om du vill använda en SMTP-åtgärd startar du logikappen med en utlösare, till exempel en Salesforce-utlösare, om du har ett Salesforce-konto.

  Du kan till exempel starta logikappen med Salesforce-utlösaren **När en post skapas.** 
  Den här utlösaren utlöses varje gång en ny post, till exempel ett lead, skapas i Salesforce. 
  Du kan sedan följa den här utlösaren med åtgärden SMTP **Skicka e-post.** På så sätt skickar logikappen ett e-postmeddelande från ditt SMTP-konto om den nya posten när den nya posten skapas.

## <a name="connect-to-smtp"></a>Ansluta till SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com)och öppna logikappen i Logic App Designer, om den inte redan är öppen.

1. Under det sista steget där du vill lägga till en SMTP-åtgärd väljer du **Nytt steg**. 

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet**+**( ) som visas och välj sedan **Lägg till en åtgärd**.

1. I sökrutan anger du "smtp" som filter. Välj den åtgärd du vill använda under åtgärdslistan.

1. Ange den här anslutningsinformationen när du uppmanas att ange:

   | Egenskap | Krävs | Beskrivning |
   |----------|----------|-------------|
   | **Anslutningsnamn** | Ja | Ett namn på anslutningen till SMTP-servern | 
   | **SMTP-serveradress** | Ja | Adressen till SMTP-servern | 
   | **Användarnamn** | Ja | Ditt användarnamn för ditt SMTP-konto | 
   | **Lösenord** | Ja | Ditt lösenord för ditt SMTP-konto | 
   | **SMTP-serverport** | Inga | En specifik port på smtp-servern som du vill använda | 
   | **Aktivera SSL?** | Inga | Aktivera eller inaktivera SSL-kryptering. | 
   |||| 

1. Ange nödvändiga uppgifter för den valda åtgärden. 

1. Spara logikappen eller fortsätt att skapa logikappens arbetsflöde.

## <a name="connector-reference"></a>Referens för anslutningsapp

Mer teknisk information om den här kopplingen, till exempel utlösare, åtgärder och begränsningar enligt beskrivningen i kopplingens Swagger-fil, finns på [kopplingens referenssida](https://docs.microsoft.com/connectors/smtpconnector/).

> [!NOTE]
> För logikappar i en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)använder den här anslutningens ISE-märkta version [ISE-meddelandegränserna](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)