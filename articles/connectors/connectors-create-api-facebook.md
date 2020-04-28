---
title: Anslut till Facebook
description: Automatisera aktiviteter och arbets flöden som hanterar din Facebook-tidslinje och-sida med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665810"
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