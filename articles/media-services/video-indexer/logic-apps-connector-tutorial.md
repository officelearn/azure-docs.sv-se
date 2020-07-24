---
title: Själv studie kursen Video Indexer kopplingar med Logic app och energi automatisering.
description: Den här självstudien visar hur du kan låsa upp nya upplevelser och försäljares möjligheter Video Indexer kopplingar med Logic app och energi automatisering.
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 5f29e616c0643914ca28921eee481105a5feb0c5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047105"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>Självstudie: använda Video Indexer med Logic app och Power automatisering

Azure Media Services [video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) stöder både server-till-Server-och klient-till-server-kommunikation och gör det möjligt för video Indexer användare att integrera video-och ljud insikter enkelt i sin program logik, låsa upp nya upplevelser och försäljarens möjligheter.

För att förenkla integrationen stöder vi [Logic Apps](https://azure.microsoft.com/services/logic-apps/)   och [automatiserade](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)   anslutningar som är kompatibla med vårt API. Du kan använda kopplingarna för att konfigurera anpassade arbets flöden för att effektivt indexera och extrahera insikter från en stor mängd video-och ljudfiler, utan att behöva skriva en enda rad kod. Genom att använda kopplingarna för din integrering får du dessutom bättre insyn i arbets flödets hälso tillstånd och ett enkelt sätt att felsöka det.  

För att hjälpa dig att komma igång snabbt med Video Indexer-kopplingar kommer vi att göra en genom gång av ett exempel på en Logic app och en automatiserad automatiserad lösning som du kan konfigurera. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ladda upp och indexera videon automatiskt
> * Konfigurera fil överförings flödet
> * Konfigurera flödet för JSON-extrahering

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att börja med behöver du också ett Video Indexer konto tillsammans med åtkomst till API: erna via API-nyckeln. 

Du måste också ha ett Azure Storage-konto. Tänk på åtkomst nyckeln för ditt lagrings konto. Skapa två behållare – en för att lagra videor i och en för att lagra insikter som genererats av Video Indexer i.  

Sedan måste du öppna två separata flöden antingen på Logic Apps eller i energi spar läge (beroende på vilket du använder).  

## <a name="upload-and-index-your-video-automatically"></a>Ladda upp och indexera videon automatiskt 

Det här scenariot består av två olika flöden som fungerar tillsammans. Det första flödet utlöses när en BLOB läggs till eller ändras i ett Azure Storage konto. Den nya filen överförs till Video Indexer med en återanrops-URL för att skicka ett meddelande när indexerings åtgärden har slutförts. Det andra flödet utlöses baserat på återanrops-URL: en och sparar de extraherade insikterna till en JSON-fil i Azure Storage. Den här två flödes metoden används för att stödja asynkron uppladdning och indexering av större filer effektivt. 

### <a name="set-up-the-file-upload-flow"></a>Konfigurera fil överförings flödet 

Det första flödet utlöses när en BLOB läggs till i din Azure Storage-behållare. När den har utlösts skapas en SAS-URI som du kan använda för att ladda upp och indexera videon i Video Indexer. Börja med att skapa följande flöde. 

![Fil överförings flöde](./media/logic-apps-connector-tutorial/file-upload-flow.png)

Om du vill konfigurera det första flödet måste du ange din Video Indexer API-nyckel och Azure Storage autentiseringsuppgifter. 

![Azure Blob Storage](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![Anslutningsnamn och API-nyckel](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

När du har anslutit till Azure Storage och Video Indexer konton går du till avsnittet "när en BLOB läggs till eller ändras) och väljer den behållare som du vill placera dina videofiler i. 

![Container](./media/logic-apps-connector-tutorial/container.png)

Gå sedan till åtgärden "skapa SAS-URI per sökväg" och välj lista över filernas sökväg från alternativen för dynamiskt innehåll.  

![SAS-URI med sökväg](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

Fyll i [kontots plats och ID](./video-indexer-use-apis.md#account-id)   för att hämta video Indexer-kontots token.

![Hämta konto åtkomst-token](./media/logic-apps-connector-tutorial/account-access-token.png)

För "Ladda upp video och index", fyller du i de obligatoriska parametrarna och video-URL: en. Välj Lägg till ny parameter och välj återanrops-URL. 

![Ladda upp och indexera](./media/logic-apps-connector-tutorial/upload-and-index.png)

Du lämnar återanrops-URL: en tom för tillfället. Du lägger bara till den när du har slutfört det andra flödet där återanrops-URL: en har skapats. 

Du kan använda standardvärdet för de andra parametrarna eller ange dem efter dina behov. 

Klicka på Spara och låt oss gå vidare till konfigurera det andra flödet för att extrahera insikterna när överföringen och indexeringen har slutförts. 

## <a name="set-up-the-json-extraction-flow"></a>Konfigurera flödet för JSON-extrahering 

När överföringen och indexeringen har slutförts från det första flödet skickas en HTTP-begäran med rätt återanrops-URL för att utlösa det andra flödet. Sedan hämtar den insikter som genereras av Video Indexer. I det här exemplet kommer den att lagra utdata från ditt indexerings jobb i din Azure Storage.  Det är dock upp till vad du kan göra med utdata.  

Skapa det andra flödet separat från det första. 

![JSON-extraherings flöde](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

Om du vill konfigurera det här flödet måste du ange din Video Indexer API-nyckel och Azure Storage autentiseringsuppgifterna igen. Du måste uppdatera samma parametrar som du gjorde för det första flödet. 

För utlösaren visas ett fält för HTTP POST-URL. URL: en genereras inte förrän du har sparat ditt flöde. du behöver dock URL: en slutligen. Vi kommer att komma tillbaka till detta. 

Fyll i [kontots plats och ID](./video-indexer-use-apis.md#account-id)   för att hämta video Indexer-kontots token.  

Gå till åtgärden "Hämta video index" och fyll i de obligatoriska parametrarna. För video-ID skriver du följande uttryck: triggerOutputs () [' frågor '] [' ID '] 

![åtgärds information för video Indexer](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

Det här uttrycket anger att Connector ska hämta video-ID: t från utlösaren. I det här fallet blir utlösaren utdata från "Ladda upp video och index" i din första utlösare. 

Gå till åtgärden "skapa BLOB" och Välj sökvägen till den mapp där du vill spara insikterna till. Ange namnet på blobben som du skapar. För BLOB-innehåll, Lägg i följande uttryck: Body (' Get_Video_Index ') 

![Skapa BLOB-åtgärd](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

Det här uttrycket tar utdata från åtgärden "Hämta video index" från det här flödet. 

Klicka på "Spara flöde". 

När flödet har sparats skapas en HTTP POST-URL i utlösaren. Kopiera URL: en från utlösaren. 

![Spara URL-utlösare](./media/logic-apps-connector-tutorial/save-url-trigger.png)

Nu går du tillbaka till det första flödet och klistrar in webb adressen i åtgärden "Ladda upp video och index" för URL-parametern för motringning. 

Se till att båda flödena sparas och att du är redo att sätta igång! 

Testa din nyligen skapade Logic app eller automatiserade automatiserade lösning genom att lägga till en video i din Azure blobs-behållare och gå tillbaka några minuter senare för att se att insikterna visas i målmappen. 

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med den här självstudien är du välkommen att hålla den här Logic-appen eller den automatiserade automatiserade lösningen i drift om du behöver. Men om du inte vill att den här körningen ska fortsätta och inte vill faktureras stänger du av båda dina flöden om du använder automatisk energi användning. Inaktivera båda flödena om du använder Logic Apps. 

## <a name="next-steps"></a>Nästa steg

Den här självstudien visade bara ett exempel på en Video Indexer-anslutning. Du kan använda Video Indexer anslutningar för alla API-anrop som tillhandahålls av Video Indexer. Exempel: Ladda upp och hämta insikter, Översätt resultaten, få inbäddnings bara widgetar och till och med anpassa dina modeller. Dessutom kan du välja att utlösa dessa åtgärder baserat på olika källor som uppdateringar av fil databaser eller e-postmeddelanden som skickas. Du kan sedan välja att uppdatera resultatet till vår relevanta infrastruktur eller program eller att generera valfritt antal åtgärds objekt.  

> [!div class="nextstepaction"]
> [Använda Video Indexer-API:et](video-indexer-use-apis.md)
