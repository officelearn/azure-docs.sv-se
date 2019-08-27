---
title: Ansluta till Facebook – Azure Logic Apps
description: 'Hantera din tids linje och sida med Facebook REST API: er och Azure Logic Apps'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 83431184d7e9c5970ece6af143ee9b5166da96d5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050948"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Hantera din Facebook-tidslinje och-sida genom att använda Azure Logic Apps

Anslut till Facebook och publicera på en tids linje, hämta en sid matning med mera. Med Facebook kan du:

* Skapa ditt affärs flöde baserat på de data du får från Facebook. 
* Använd en utlösare när ett nytt inlägg tas emot.
* Använd åtgärder som publicerar på din tids linje, hämta en sid matning med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. Om det till exempel finns ett nytt inlägg på din tids linje kan du ta det inlägget och skicka det till ditt Twitter-flöde. 

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Skapa en anslutning till Facebook

När du lägger till den här anslutningen i dina Logi Kap par måste du auktorisera Logic Apps för att ansluta till Facebook.

1. Logga in på ditt Facebook-konto.

2. Välj **auktorisera**och Tillåt att dina Logi Kap par ansluter och använder din Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare, åtgärder och gränser, som beskrivs i filens OpenAPI-fil (tidigare Swagger), finns på [kopplingens referens sida](/connectors/facebook/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)