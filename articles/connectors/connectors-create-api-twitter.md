---
title: "Lär dig hur du använder Twitter-anslutningen i logikappar | Microsoft Docs"
description: "Översikt över Twitter-anslutningen med REST API-parametrar"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 065de976118e7be67ef8a515e39c04cfd74b5b43
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="get-started-with-the-twitter-connector"></a>Kom igång med Twitter-anslutningen
Med Twitter-anslutningen kan du:

* Efter tweets och få tweets
* Åtkomst tidslinjer, vänner och blandare
* Utför någon av de andra åtgärder och utlösare som beskrivs nedan  

Att använda [alla anslutningar](apis-list.md), måste du först skapa en logikapp. Du kan komma igång med [att skapa en logikapp nu](../logic-apps/quickstart-create-first-logic-app-workflow.md).  

## <a name="connect-to-twitter"></a>Ansluta till Twitter
Innan din logikapp kan komma åt någon tjänst, måste du först skapa en *anslutning* till tjänsten. En [anslutning](connectors-overview.md) tillhandahåller anslutningen mellan en logikapp och en annan tjänst.  

### <a name="create-a-connection-to-twitter"></a>Skapa en anslutning till Twitter
> [!INCLUDE [Steps to create a connection to Twitter](../../includes/connectors-create-api-twitter.md)]
> 
> 

## <a name="use-a-twitter-trigger"></a>Använda en Twitter-utlösare
En utlösare är en händelse som kan användas för att starta arbetsflödet som definierats i en logikapp. [Mer information om utlösare](../logic-apps/logic-apps-overview.md#logic-app-concepts).

I det här exemplet jag visar hur du använder den **när en ny tweet skickas** trigger för att söka efter #Seattle och om #Seattle hittas, uppdaterar en fil på Dropbox med text från tweet. I enterprise exempelvis kan du söka efter namnet på ditt företag och uppdatera en SQL-databas med text från tweet.

1. Ange *twitter* i sökrutan på logic apps designer väljer den **Twitter - när en ny tweet skickas** utlösare   
   ![Twitter-utlösarbild 1](./media/connectors-create-api-twitter/trigger-1.png)  
2. Ange *#Seattle* i den **söktext** kontroll  
   ![Bild 2 till Twitter-utlösare](./media/connectors-create-api-twitter/trigger-2.png) 

Din logikapp har nu konfigurerats med en utlösare som startar andra utlösare och åtgärder i arbetsflödet körs. 

> [!NOTE]
> Det måste innehålla minst en utlösare och en åtgärd för en logikapp ska fungera. Följ stegen i nästa avsnitt för att lägga till en åtgärd.  
> 
> 

## <a name="add-a-condition"></a>Lägg till ett villkor
Eftersom vi bara är intresserad av tweets från användare med mer än 50 användare läggas ett villkor som bekräftar antalet blandare till logikappen.  

1. Välj **+ nytt steg** att lägga till den åtgärd som ska vidtas när #Seattle finns i en ny tweet  
   ![Twitter-åtgärd bild 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
2. Välj den **Lägg till ett villkor** länk.  
   ![Twitter villkoret bild 1](../../includes/media/connectors-create-api-twitter/condition-1.png)   
   Då öppnas den **villkoret** kontroll där du kan kontrollera villkor som *är lika med*, *är mindre än*, *är större än*, *innehåller*osv.  
   ![Bild 2 till Twitter villkor](../../includes/media/connectors-create-api-twitter/condition-2.png)   
3. Välj den **väljer ett värde** kontroll.  
   I den här kontrollen kan du välja en eller flera egenskaper från tidigare åtgärder eller utlösare som värde vars villkoret utvärderas till true eller false.
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
   Villkor läggs nu. Spara ditt arbete med hjälp av den **spara** länk på menyn ovan.    
   ![Twitter villkoret bild 8](../../includes/media/connectors-create-api-twitter/condition-8.png)   

## <a name="use-a-twitter-action"></a>Använda en Twitter-åtgärd
En åtgärd är en åtgärd som utförs av arbetsflödet som definierats i en logikapp. [Mer information om åtgärder](../logic-apps/logic-apps-overview.md#logic-app-concepts).  

Nu när du har lagt till en utlösare, Följ dessa steg för att lägga till en åtgärd som kommer efter nya tweets med innehållet i tweets som hittades av utlösaren. Endast tweets från användare med mer än 50 blandare för den här genomgången kommer att publiceras.  

I nästa steg ska du lägga till en Twitter-åtgärd som kommer efter tweets med hjälp av vissa egenskaper för varje tweet som har publicerats av en användare som har fler än 50 blandare.  

1. Välj **lägga till en åtgärd**. Då öppnas sökkontrollen där du kan söka efter andra åtgärder och utlösare.  
   ![Twitter villkoret bild 9](../../includes/media/connectors-create-api-twitter/condition-9.png)   
2. Ange *twitter* i sökrutan väljer den **Twitter - efter tweets** åtgärd. Då öppnas den **efter tweets** styra där du ska ange all information för tweet att anslås.      
   ![Twitter-åtgärd bild 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)   
3. Välj den **Twittra text** kontroll. Alla utdata från tidigare åtgärder och utlösare i logikappen visas nu. Du kan välja något av dessa och använda dem som en del av texten tweet för nya tweet.     
   ![Bild 2 till Twitter åtgärd](../../includes/media/connectors-create-api-twitter/action-2.png)   
4. Välj **användarnamn**   
5. Ange *säger:* i kontrollen tweet text. Gör det bara efter användarnamn.  
6. Välj *Twittra text*.       
   ![Bild 3 till Twitter åtgärd](../../includes/media/connectors-create-api-twitter/action-3.png)   
7. Spara ditt arbete och skickar tweets med #Seattle hashtaggar att aktivera arbetsflödet.  


## <a name="connector-specific-details"></a>Connector-specifik information

Visa alla utlösare och åtgärder som definierats i swagger och även se några gränser i den [connector information](/connectors/twitterconnector/). 

## <a name="next-steps"></a>Nästa steg
[Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md)

