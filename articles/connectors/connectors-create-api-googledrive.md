---
title: Anslut till Google Drive
description: Automatisera arbets flöden som skapar och hanterar filer för Google Drive med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666847"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Skapa och hantera filer för Google Drive med hjälp av Azure Logic Apps

Anslut till Google Drive för att skapa filer, hämta rader med mera. Med Google Drive kan du: 

* Skapa ditt affärs flöde baserat på de data du får från din sökning. 
* Använd åtgärder för att söka efter bilder, Sök efter nyheter och mycket mer. De här åtgärderna får ett svar och gör sedan resultatet tillgängligt för andra åtgärder. Du kan till exempel söka efter en video och sedan använda Twitter för att publicera videon i ett Twitter-flöde.

Du kan komma igång genom att skapa en Logic app nu, se [skapa en Logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-the-connection-to-google-drive"></a>Skapa anslutningen till Google Drive

När du lägger till den här anslutningen i dina Logi Kap par måste du tillåta att Logic Apps ansluter till din Google Drive.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

När du har skapat anslutningen anger du Google Drive-egenskaperna, t. ex. mappsökvägen eller fil namnet. 

## <a name="connector-specific-details"></a>Anslutningsspecifika Detaljer

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens Swagger beskrivning, finns på [kopplingens referens sida](/connectors/googledrive/).

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om andra [Logic Apps anslutningar](apis-list.md)
