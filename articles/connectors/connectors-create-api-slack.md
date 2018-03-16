---
title: "Använd Slack-kopplingen i dina Azure logic apps | Microsoft Docs"
description: Anslut till Slack i dina logic apps
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 73c512c70f1c135bd791d93cecc42bd6f4c06b3d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-slack-connector"></a>Kom igång med Slack-koppling
Slack är ett team kommunikationsverktyg som sammanför alla meddelanden i ett team placera, direkt sökbara och tillgänglig överallt. 

Kom igång genom att skapa en logikapp nu. Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-slack"></a>Skapa en anslutning till Slack
Om du vill använda Slack-anslutningstjänsten måste du först skapa en **anslutning** ange detaljer för dessa egenskaper: 

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Ange autentiseringsuppgifter för Slack |

Logga in till Slack med följande steg och slutför konfigurationen av slacket **anslutning** i din logikapp:

1. Välj **upprepning**
2. Välj en **frekvens** och ange en **intervall**
3. Välj **lägga till en åtgärd**  
   ![Konfigurera Slack][1]  
4. Ange Slack i sökrutan och vänta tills sökning för att returnera alla poster med Slack i namnet
5. Välj **Slack - skicka meddelandet**
6. Välj **logga in till Slack**:  
   ![Konfigurera Slack][2]
7. Ange din Slack autentiseringsuppgifter för att logga in att godkänna programmet    
   ![Konfigurera Slack][3]  
8. Du ska omdirigeras till inloggningssidan för din organisation. **Auktorisera** Slack interagerar med din logikapp:      
   ![Konfigurera Slack][5] 
9. När tillståndet är klar är du omdirigeras till din logikapp för att slutföra den genom att konfigurera den **Slack - hämta alla meddelanden** avsnitt. Lägg till andra utlösare och åtgärder som du behöver.  
   ![Konfigurera Slack][6]
10. Spara ditt arbete genom att välja **spara** på menyn (mot upp).

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/slack/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
