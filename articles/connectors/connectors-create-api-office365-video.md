---
title: Ansluta till Office 365 Video
description: Automatisera uppgifter och arbetsflöden som hanterar videor i Office 365 Video med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665793"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Hantera videor i Office365 Video med Hjälp av Azure Logic Apps

Anslut till Office 365 Video för att få information om en Office 365-video, få en lista med videoklipp med mera. Med Office 365 Video kan du:

* Skapa ditt affärsflöde baserat på de data du får från Office 365 Video. 

* Använd åtgärder som kontrollerar videoportalstatus, få en lista över all video i en kanal med mera. Dessa åtgärder får ett svar och gör sedan utdata tillgängliga för andra åtgärder. 

Du kan till exempel använda Bing Search-kopplingen för att söka efter Office 365-videor och sedan använda videokontakten i Office 365 för att få information om videon. Om videon uppfyller dina krav kan du publicera den här videon på Facebook.

Du kan komma igång genom att skapa en logikapp nu, se [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Ansluta till Office365 Video

När du lägger till den här anslutningsappen i logikapparna måste du logga in på ditt Office 365 Video-konto och tillåta att logikappar ansluter till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

När du har skapat anslutningen anger du de Office 365-videoegenskaperna, till exempel klientnamnet eller kanal-ID:t. 

## <a name="connector-specific-details"></a>Anslutningsspecifik information

Visa alla utlösare och åtgärder som definierats i skryt, och se även eventuella gränser i [anslutningsinformationen](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps-kopplingar](../connectors/apis-list.md)