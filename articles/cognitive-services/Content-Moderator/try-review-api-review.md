---
title: Hantera innehåll med hjälp av mänsklig granskning med API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skapar mänsklig granskning i Content Moderator-API-konsolen.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: bb95341a09f09ce8020f34476e720270fd401909
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219761"
---
# <a name="create-reviews-from-the-api-console"></a>Skapa granskningar från API-konsol

Använd API för granska [granska åtgärder](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) Skapa bild eller text granskningar för mänskliga moderering. Mänskliga moderatorer Använd granskningsverktyget för att granska innehållet. Använd den här åtgärden baserat på affärslogiken efter moderering. Använda den när du har sökt igenom ditt innehåll med hjälp av Content Moderator-bild eller text API: er eller andra Cognitive Services-API: er. 

När en mänsklig moderator granskar automatiskt tilldelade taggar och förutsägelsedata och skickar ett sista moderering beslut, skickar granska API: et all information som ditt API-slutpunkten.

## <a name="use-the-api-console"></a>Använd API-konsol
Om du vill testa API: et med hjälp av konsolen online, behöver du några värden för att komma in i konsolen:

- **teamName**: på teamnamnet som du skapade när du konfigurerar ditt konto för granskning-verktyget. 
- **ContentId**: den här strängen skickades till API: et och returneras via motringningen. ContentId är användbart för att associera interna identifierare eller metadata med resultatet från ett jobb för moderering.
- **Metadata**: anpassad nyckel / värde-par som returneras till API-slutpunkten under motringningen. Om nyckeln är en kort kod som har definierats i granskningsverktyget, visas den som en etikett.
- **OCP-Apim-Subscription-Key**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

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
