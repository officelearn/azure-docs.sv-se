---
title: Anslut till Office 365-Video
description: Automatisera uppgifter och arbets flöden som hanterar videor i Office 365-Video med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665793"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Hantera videor i Office365-video med hjälp av Azure Logic Apps

Anslut till Office 365-Video för att få information om en Office 365-Video, hämta en lista över videor och mycket annat. Med Office 365-Video kan du:

* Skapa ditt affärs flöde baserat på de data du får från Office 365-videon. 

* Använd åtgärder som kontrollerar video portalens status, hämtar en lista över all video i en kanal med mera. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. 

Du kan till exempel använda Bing-sökning Connector för att söka efter Office 365-videor och sedan använda Office 365 Video Connector för att få information om videon. Om videon uppfyller dina krav kan du publicera den här videon på Facebook.

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Anslut till Office365-video

När du lägger till den här anslutningen i dina Logi Kap par måste du logga in på ditt Office 365-videokonto och tillåta att Logic Apps ansluter till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

När du har skapat anslutningen anger du Office 365-Video egenskaperna, t. ex. klient organisations namn eller kanal-ID. 

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Visa eventuella utlösare och åtgärder som definierats i Swagger och se även eventuella begränsningar i [anslutnings informationen](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)