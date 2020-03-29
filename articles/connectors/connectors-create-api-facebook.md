---
title: Anslut till Facebook
description: Automatisera uppgifter och arbetsflöden som hanterar din Facebook-tidslinje och -sida med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665810"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Hantera din Facebook-tidslinje och -sida med hjälp av Azure Logic Apps

Anslut till Facebook och publicera på en tidslinje, få ett sidflöde med mera. Med Facebook kan du:

* Bygg upp ditt affärsflöde baserat på de data du får från Facebook. 
* Använd en utlösare när ett nytt inlägg tas emot.
* Använd åtgärder som publicerar på din tidslinje, få ett sidflöde med mera. Dessa åtgärder får ett svar och gör sedan utdata tillgängliga för andra åtgärder. När det till exempel finns ett nytt inlägg på tidslinjen kan du ta det inlägget och skicka det till ditt Twitter-flöde. 

Du kan komma igång genom att skapa en logikapp nu, se [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Skapa en anslutning till Facebook

När du lägger till den här kopplingen i logikapparna måste du auktorisera logikappar för att ansluta till din Facebook.

1. Logga in på ditt Facebook-konto.

2. Välj **Auktorisera**och tillåt dina logikappar att ansluta och använda din Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och begränsningar, enligt beskrivningen i kopplingens OpenAPI-fil (tidigare Swagger) finns på [kopplingens referenssida](/connectors/facebook/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)