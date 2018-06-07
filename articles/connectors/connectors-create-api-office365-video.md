---
title: Ansluta till Office 365 Video - Azure Logikappar | Microsoft Docs
description: 'Hantera videor med Office 365 Video REST API: er och Azure Logic Apps'
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: ef56159735654cd5cefbba99ee9cead42c81086b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34610348"
---
# <a name="get-started-with-the-office365-video-connector"></a>Kom igång med Office365 Video-anslutning
Ansluta till Office 365 Video om du vill få information om en Office 365 video hämta en lista över filmer och mycket annat. Med Office 365 Video kan du:

* Skapa ditt företag flödet som baseras på de data som du får från Office 365 Video. 
* Använd åtgärder som statuskontroll video portal, hämta en lista över alla video i en kanal med mera. De här åtgärderna få svar och utdata gör tillgängligt för andra åtgärder. Du kan till exempel använda Bing Search-anslutningen för att söka efter Office 365-videor och sedan använda Office 365 videoutgång för att få information om att videon. Om videon uppfyller dina krav, kan du publicera den här videon på Facebook. 

Du kan komma igång genom att skapa en logikapp nu, se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office365-video-connector"></a>Skapa en anslutning till Office365 Video-anslutning
När du lägger till den här anslutningen dina logic apps kan måste du logga in på ditt Office 365 Video-konto och Tillåt logikappar att ansluta till ditt konto.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]
> 
> 

När du skapar anslutningen kan du ange Office 365 video egenskaperna, som innehavarens namn eller kanaler ID. 


## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/office365videoconnector/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).