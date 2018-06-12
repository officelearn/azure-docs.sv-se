---
title: Lär dig hur du använder Twitter-anslutningen i logikappar | Microsoft Docs
description: Översikt över Twitter-anslutningen med REST API-parametrar
services: ''
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: b44a973a94043f71f2fd9803abca47652363d8a1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296551"
---
# <a name="get-started-with-the-twitter-connector"></a>Kom igång med Twitter-anslutningen
Med Twitter-anslutningen kan du:

* Efter tweets och få tweets
* Åtkomst tidslinjer, vänner och blandare
* Utför någon av de andra åtgärder och utlösare som beskrivs i den här artikeln

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/quickstart-create-first-logic-app-workflow.md).  

## <a name="connect-to-twitter"></a>Ansluta till Twitter
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

### <a name="create-a-connection-to-twitter"></a>Skapa en anslutning till Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Använda en Twitter-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

I det här exemplet använder du den **när en ny tweet skickas** trigger för att söka efter #Seattle. Och om #Seattle hittas, uppdaterar du en fil i Dropbox med text från tweet. I enterprise exempelvis kan du söka efter namnet på ditt företag och uppdatera en SQL-databas med text från tweet.

1. Ange *twitter* i sökrutan på logic apps designer väljer den **Twitter - när en ny tweet skickas** utlösare   
   ![Twitter-utlösarbild 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Ange *#Seattle* i den **söktext** kontroll  
   ![Bild 2 till Twitter-utlösare](./media/connectors-create-api-twitter/trigger-2.png) 

Din logikapp har nu konfigurerats med en utlösare som startar andra utlösare och åtgärder i arbetsflödet körs. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Använd stegen i nästa avsnitt om du vill lägga till en åtgärd.

## <a name="add-a-condition"></a>Lägg till ett villkor
Vi är bara intresserad av tweets från användare med mer än 50 användare. Så, ett villkor som bekräftar antalet blandare har lagts till logikappen.  

1. Välj **+ nytt steg** att lägga till den åtgärd som ska vidtas när #Seattle finns i en ny tweet  
   ![Twitter-åtgärd bild 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Välj den **Lägg till ett villkor** länk.  
   ![Twitter villkoret bild 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Då öppnas den **villkoret** kontroll där du kan kontrollera villkor som *är lika med*, *är mindre än*, *är större än*, *innehåller*osv.  
   ![Bild 2 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Välj den **väljer ett värde** kontroll. I den här kontrollen kan du välja en eller flera av egenskaperna från tidigare åtgärder eller utlösare. Egenskapsvärdet villkoret utvärderas till true eller false.
   ![Bild 3 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-3.png)   
4. Välj den **...**  att expandera listan över egenskaper så att du kan se alla egenskaper som är tillgängliga.        
   ![Bild 4 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-4.png)   
5. Välj den **blandare antal** egenskapen.    
   ![Bild 5 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-5.png)   
6. Lägg märke till egenskapen blandare antal nu i värdekontrollen.    
   ![Bild 6 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-6.png)   
7. Välj **är större än** från listan över operatörer.    
   ![Bild 7 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-7.png)   
8. Ange 50 som operand för den *är större än* operator.  
   Villkor läggs nu. Spara ditt arbete med hjälp av den **spara** länk på menyn.    
   ![Twitter villkoret bild 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Använda en Twitter-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Det finns en utlösare, lägger du till en åtgärd som publicerar en ny tweet med innehållet i tweets som hittades av utlösaren. Endast tweets från användare med mer än 50 blandare är satt till för den här genomgången.  

Lägg till ett Twitter-åtgärd som skickar tweets med vissa av egenskaperna för varje tweet som publiceras av en användare med mer än 50 blandare i nästa steg.  

1. Välj **lägga till en åtgärd**. Det här steget öppnas sökkontrollen där du kan söka efter andra åtgärder och utlösare.  
   ![Twitter villkoret bild 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Ange *twitter* i sökrutan väljer den **Twitter - efter tweets** åtgärd. Det här steget öppnar den **efter tweets** styra där du kan ange all information för tweet att anslås.      
   ![Twitter-åtgärd bild 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Välj den **Twittra text** kontroll. Alla utdata från tidigare åtgärder och utlösare i logikappen visas nu. Du kan välja något av dessa utdata och använda dem som en del av texten tweet för nya tweet.     
   ![Bild 2 till Twitter åtgärd](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Välj **användarnamn**   
5. Omedelbart efter användarnamn, ange *säger:* i kontrollen tweet text.
6. Välj *Twittra text*.       
   ![Bild 3 till Twitter åtgärd](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Aktivera ditt arbetsflöde genom att spara ditt arbete och skickar tweets med #Seattle hashtaggar.


## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)