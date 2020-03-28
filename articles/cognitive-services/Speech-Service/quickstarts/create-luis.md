---
title: 'Snabbstart: Skapa en LUIS-nyckel'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten får du lära dig hur du skapar ett LUIS-program och får en nyckel.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 5b1a5ac4867379457d161d07f4f4f2fc2d8ee6c3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77119591"
---
# <a name="quickstart-getting-a-luis-endpoint-key"></a>Snabbstart: Hämta en LUIS-slutpunktsnyckel

## <a name="prerequisites"></a>Krav

Se till att du har följande objekt innan du börjar den här självstudien:

* Ett LUIS-konto. Du kan skaffa ett kostnadsfritt via [LUIS-portalen](https://www.luis.ai/home).

## <a name="luis-and-speech"></a>LUIS och tal

LUIS integreras med Speech-tjänsten för att identifiera avsikter från tal. Du behöver inte en Speech-tjänstprenumeration, bara LUIS.

LUIS använder tre typer av nycklar:

|Nyckeltyp|Syfte|
|--------|-------|
|Redigering|Gör att du kan skapa och ändra LUIS-appar programmässigt|
|Starter|Gör att du kan testa LUIS-programmet med endast text|
|Slutpunkt |Auktoriserar åtkomst till en viss LUIS-app|

För den här självstudien behöver du nyckeltypen slutpunkt. Självstudien använder exemplet Home Automation LUIS-appen, som du kan skapa genom att följa snabbstarten [Fördefinierad hemautomatisering.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app) Om du har skapat en egen LUIS-app kan du använda den i stället.

När du skapar en LUIS-app genererar LUIS automatiskt en startnyckel så att du kan testa appen med hjälp av textfrågor. Den här nyckeln aktiverar inte integrering av taltjänsten och fungerar inte med den här självstudien. Skapa en LUIS-resurs i Azure-instrumentpanelen och tilldela den till LUIS-appen. Du kan använda den kostnadsfria prenumerationsnivån för den här självstudiekursen.

När du har skapat LUIS-resursen i Azure-instrumentpanelen loggar du in på [LUIS-portalen,](https://www.luis.ai/home)väljer ditt program på sidan **Mina appar** och växlar sedan till appens **hantera-sida.** Slutligen väljer du **Nycklar och slutpunkter** i sidofältet.

![LUIS-portalens inställningar för nycklar och slutpunkter](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-endpoints-page.png)

På sidan **Inställningar för nycklar och slutpunkt:**

1. Bläddra ned till avsnittet **Resurser och nycklar** och välj Tilldela **resurs**.
1. Gör följande ändringar i dialogrutan **Tilldela en nyckel till appen:**

   * Välj **Microsoft**under **Klient**.
   * Under **Prenumerationsnamn**väljer du den Azure-prenumeration som innehåller den LUIS-resurs som du vill använda.
   * Under **Nyckel**väljer du den LUIS-resurs som du vill använda med appen.

   Om en stund visas den nya prenumerationen i tabellen längst ned på sidan.

1. Markera ikonen bredvid en tangent för att kopiera den till Urklipp. (Du kan använda valfri nyckel.)

![Prenumerationsnycklar för LUIS-app](~/articles/cognitive-services/Speech-Service/media/sdk/luis-keys-assigned.png)


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Identifiera avsikter](~/articles/cognitive-services/Speech-Service/quickstarts/intent-recognition.md)
