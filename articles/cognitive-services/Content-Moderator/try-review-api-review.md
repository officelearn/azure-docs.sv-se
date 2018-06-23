---
title: Måttlig innehåll med hjälp av mänsklig granskningar Azure innehåll kontrollant | Microsoft Docs
description: Lär dig hur du skapar mänsklig granskningar i innehåll kontrollant API-konsolen.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: e9faf595e65ba4475a743e4cb45919fd30fbd6e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351579"
---
# <a name="create-reviews-from-the-api-console"></a>Skapa granskningar från konsolen API

Använd API för granska [granska operations](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) att skapa bild eller text granskning av mänsklig måtta. Använda mänsklig moderatorer i verktyget granska för att granska innehållet. Använd den här åtgärden baserat på affärslogiken efter måtta. Använda den när du har sökt igenom ditt innehåll med hjälp av innehåll kontrollant avbildningen eller text API: er eller andra kognitiva Services API: er. 

När en mänsklig kontrollant granskar automatiskt tilldelade taggar och förutsägelse data och skickar en slutlig avbrottsmoderering beslut, skickar granska API all information till API-slutpunkt.

## <a name="use-the-api-console"></a>Använda API-konsolen
Om du vill testa API: et med hjälp av konsolen online, behöver du några värden du anger i konsolen:

- **teamName**: Teamnamn som du skapade när du konfigurerar kontot för att granska verktyget. 
- **ContentId**: denna sträng skickades till API: et och returneras via återanropet. ContentId är användbart för att associera interna identifierare eller metadata med resultatet av en avbrottsmoderering jobbet.
- **Metadata**: anpassad nyckel-värdepar som returneras till API-slutpunkt under återanropet. Om nyckeln är en kort kod som har definierats i verktyget granska, visas den som en etikett.
- **OCP-Apim-prenumeration-nyckeln**: finns på den **inställningar** fliken. Mer information finns i [översikt](overview.md).

Det enklaste sättet att komma åt en testnings-konsol är från den **autentiseringsuppgifter** fönster.

1.  I den **autentiseringsuppgifter** väljer [granska API-referens för](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

  Den **granska - skapa** öppnas.

2.  För **öppna API-testet konsolen**, väljer du den region som bäst beskriver din plats.

  ![Granska – skapa val av region](images/test-drive-region.png)

  Den **granska - skapa** API-konsolen öppnas.
  
3.  Ange värden för obligatoriska frågeparametrar content-type och din prenumeration nyckel. I den **Begärandetext** anger innehållet (till exempel bildplatsen), metadata och annan information som associeras med innehållet.

  ![Granska – skapa konsolen frågeparametrar, rubriker och begäran brödtext rutan](images/test-drive-review-1.PNG)
  
4.  Välj **Skicka**. Granska-ID skapas. Kopiera detta ID ska användas i följande steg.

  ![Granska – skapa konsolen svar innehåll visar granska-ID](images/test-drive-review-2.PNG)
  
5.  Välj **hämta**, och sedan öppna API: et genom att välja knappen som matchar din region. Ange värden för på sidan resulterande **teamName**, **ReviewID**, och **prenumeration nyckeln**. Välj den **skicka** på sidan. 

  ![Granska – skapa konsolen Get-resultat](images/test-drive-review-3.PNG)
  
6.  Du kommer att se resultatet av genomsökningen.

  ![Granska – skapa konsolen svar innehållsrutan](images/test-drive-review-4.PNG)
  
7.  På innehåll kontrollant-instrumentpanelen väljer **granska** > **bild**. Bilden som du har läst visas, redo för mänsklig granskning.

  ![Granska verktyget bild av en fotboll](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Nästa steg

Använda REST-API i koden eller starta med den [granskningar .NET quickstart](moderation-reviews-quickstart-dotnet.md) att integrera med ditt program.
