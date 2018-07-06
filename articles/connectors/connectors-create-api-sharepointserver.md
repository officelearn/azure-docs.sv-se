---
title: Om du använder SharePoint Server-anslutningen i Logic Apps | Microsoft Docs
description: Komma igång med SharePoint Server-anslutningen i dina Logic apps
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 3ac3105231017cdbf8bfcf143b26451a68da5283
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868709"
---
# <a name="get-started-with-the-sharepoint-connector"></a>Kom igång med SharePoint-anslutningsappen
SharePoint-Anslutningsappen är ett sätt att arbeta med listor på SharePoint.

Kom igång genom att skapa en logikapp; Se [skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-sharepoint"></a>Skapa en anslutning till SharePoint
För att använda SharePoint-Anslutningsappen måste du först skapa en **anslutning** anger du informationen för dessa egenskaper: 

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Token |Ja |Tillhandahåll autentiseringsuppgifter för SharePoint |

Att ansluta till **SharePoint**, ange din identitet (användarnamn och lösenord, autentiseringsuppgifter för smartkort och så vidare). När du har autentiserats kan fortsätta du att använda SharePoint-anslutningsappen i logikappen. 

I designer av din logikapp, Använd följande steg för att logga in och skapa den **anslutning** för användning i din logikapp:

1. Ange SharePoint i sökrutan och vänta tills sökning för att returnera alla poster med SharePoint i namnet:   
   ![Konfigurera SharePoint][1]  
2. Välj **SharePoint – när en fil har skapats**   
3. Välj **logga in på SharePoint**:   
   ![Konfigurera SharePoint][2]    
4. Ange dina autentiseringsuppgifter för SharePoint för att logga in att autentisera med SharePoint   
   ![Konfigurera SharePoint][3]     
5. När autentiseringen är klar är du omdirigeras till din logikapp för att slutföra den genom att konfigurera SharePoint- **när en fil skapas** dialogrutan.          
   ![Konfigurera SharePoint][4]  
6. Du kan sedan lägga till andra utlösare och åtgärder som du behöver för att slutföra din logikapp.   
7. Spara ditt arbete genom att välja **spara** på menyn (överst).

## <a name="connector-specific-details"></a>Information om specifika

Visa alla utlösare och åtgärder som definierats i swagger och får även eventuella gränser i den [anslutningsinformationen](/connectors/sharepoint/).

## <a name="more-connectors"></a>Fler anslutningsappar
Gå tillbaka till den [API: er lista](apis-list.md).

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png
