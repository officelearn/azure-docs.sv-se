---
title: Anslut till Office 365-Video
description: 'Hantera videor med Office 365 Video REST-API: er och Azure Logic Apps'
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: bf3c7c4faac3fcd4dd0c3f368fbe18a224ed591b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789500"
---
# <a name="get-started-with-the-office365-video-connector"></a>Kom igång med Office365 video Connector

Anslut till Office 365-Video för att få information om en Office 365-Video, hämta en lista över videor och mycket annat. Med Office 365-Video kan du:

* Skapa ditt affärs flöde baserat på de data du får från Office 365-videon. 

* Använd åtgärder som kontrollerar video portalens status, hämtar en lista över all video i en kanal med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. 

Du kan till exempel använda Bing-sökning Connector för att söka efter Office 365-videor och sedan använda Office 365 Video Connector för att få information om videon. Om videon uppfyller dina krav kan du publicera den här videon på Facebook.

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Skapa en anslutning till Office365 video Connector

När du lägger till den här anslutningen i dina Logi Kap par måste du logga in på ditt Office 365-videokonto och tillåta att Logic Apps ansluter till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

När du har skapat anslutningen anger du Office 365-Video egenskaperna, t. ex. klient organisations namn eller kanal-ID. 

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)