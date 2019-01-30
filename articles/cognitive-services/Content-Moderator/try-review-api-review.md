---
title: Måttlig innehåll med hjälp av mänsklig granskning med API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Använd granska API-granska åtgärderna för att skapa bild eller text granskningar för mänskliga moderering.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e4c750e7470c6a76e56bd67d6a134b1b61535eb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218398"
---
# <a name="create-reviews-from-the-api-console"></a>Skapa granskningar från API-konsol

Använd API för granska [granska åtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) Skapa bild eller text granskningar för mänskliga moderering. Mänskliga moderatorer Använd granskningsverktyget för att granska innehållet. Använd den här åtgärden baserat på affärslogiken efter moderering. Använda den när du har sökt igenom ditt innehåll med hjälp av Content Moderator-bild eller text API: er eller andra Cognitive Services-API: er. 

När en mänsklig moderator granskar automatiskt tilldelade taggar och förutsägelsedata och skickar ett sista moderering beslut, skickar granska API: et all information som ditt API-slutpunkten.

## <a name="use-the-api-console"></a>Använd API-konsol
Om du vill testa API: et med hjälp av konsolen online, behöver du några värden för att komma in i konsolen:

- **teamName**: På teamnamnet som du skapade när du konfigurerar ditt konto för granskning-verktyget. 
- **ContentId**: Den här strängen skickas till API: et och returneras via motringningen. ContentId är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb för moderering.
- **Metadata**: Anpassad nyckel / värde-par tillbaka till API-slutpunkten under motringningen. Om nyckeln är en kort kod som har definierats i granskningsverktyget, visas den som en etikett.
- **Ocp-Apim-Subscription-Key**: På den **inställningar** fliken. Mer information finns i [Översikt](overview.md).

Det enklaste sättet att komma åt en testkonsolen är från den **autentiseringsuppgifter** fönster.

1.  I den **autentiseringsuppgifter** väljer [granska API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  Den **granska – skapa** öppnas.

2.  För **Open API testkonsolen**, väljer du den region som bäst beskriver din plats.

  ![Granska – skapa val av region](images/test-drive-region.png)

  Den **granska – skapa** API-konsolen öppnas.
  
3.  Ange värden för obligatoriska parametrar, innehållstyp och din prenumerationsnyckel. I den **Begärandetext** anger innehållet (till exempel Bildplats), metadata och annan information som associeras med innehåll.

  ![Granska – skapa konsolen frågeparametrar, rubriker och begäran text box](images/test-drive-review-1.PNG)
  
4.  Välj **Skicka**. Åtkomstgransknings-ID skapas. Kopiera detta ID ska användas i följande steg.

  ![Granska – skapa konsolen svar innehåll visar åtkomstgransknings-ID](images/test-drive-review-2.PNG)
  
5.  Välj **hämta**, och sedan öppna API: et genom att välja knappen som matchar din region. Ange värden för på sidan resulterande **teamName**, **ReviewID**, och **prenumerationsnyckel**. Välj den **skicka** på sidan. 

  ![Granska – skapa konsolen Get-resultat](images/test-drive-review-3.PNG)
  
6.  Du ser resultatet av genomsökningen.

  ![Granska – skapa konsolen svar innehållsrutan](images/test-drive-review-4.PNG)
  
7.  Content Moderator-instrumentpanelen, klicka **granska** > **bild**. Bilden som du skannade visas, redo för mänsklig granskning.

  ![Kontrollera att verktyget avbildning av en fotboll](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller börja med den [Snabbstart för granskningar .NET](moderation-reviews-quickstart-dotnet.md) att integrera med ditt program.
