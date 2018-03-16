---
title: "Använda SharePoint Server-anslutningen i dina Logic Apps | Microsoft Docs"
description: "Komma igång med den SharePoint-Server-anslutningen i dina Logic apps"
services: logic-apps
documentationcenter: 
author: ecfan
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: d342b3c4f84c5dab212b9327d6a72759934d0ae5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-sharepoint-connector"></a>Kom igång med SharePoint-koppling
SharePoint-kopplingen innehåller ett sätt att arbeta med listor i SharePoint.

Kom igång genom att skapa en logikapp; Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Skapa en anslutning till SharePoint
Om du vill använda SharePoint-anslutningstjänsten måste du först skapa en **anslutning** ange detaljer för dessa egenskaper: 

| Egenskap | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Ange autentiseringsuppgifter för SharePoint |

Att ansluta till **SharePoint**, ange din identitet (användarnamn och lösenord, autentiseringsuppgifter för smartkort och så vidare). När du har autentiserats, kan du fortsätta att använda SharePoint-kopplingen i din logikapp. 

Använd följande steg när du är på din logikapp kan logga in och skapa den **anslutning** för användning i din logikapp:

1. Ange SharePoint i sökrutan och vänta tills sökning för att returnera alla poster med SharePoint i namnet:   
   ![Konfigurera SharePoint][1]  
2. Välj **SharePoint - när en fil har skapats**   
3. Välj **logga in på SharePoint**:   
   ![Konfigurera SharePoint][2]    
4. Ange din SharePoint-autentiseringsuppgifter för att logga in att autentisera med SharePoint   
   ![Konfigurera SharePoint][3]     
5. När autentiseringen är klar är du omdirigeras till din logikapp för att slutföra den genom att konfigurera SharePoint- **när en fil skapas** dialogrutan.          
   ![Konfigurera SharePoint][4]  
6. Du kan sedan lägga till andra utlösare och åtgärder som behövs för att slutföra din logikapp.   
7. Spara ditt arbete genom att välja **spara** på menyn (mot upp).

## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/sharepoint/).

## <a name="more-connectors"></a>Flera kopplingar
Gå tillbaka till den [API: er listan](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
