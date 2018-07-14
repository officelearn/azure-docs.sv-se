---
title: 'Skapa ett konto för Cognitive Services API: er i Azure portal | Microsoft Docs'
description: 'Så här skapar du ett konto med Microsoft Cognitive Services API: er i Azure-portalen.'
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036167"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Skapa ett konto för Cognitive Services API: er i Azure portal

Om du vill använda Microsoft Cognitive Service API: er, måste du först skapa ett konto i Azure-portalen.

1. Logga in på [Azure-portalen](http://portal.azure.com).

2. Klicka på **+ skapa en resurs**.

3. Under Azure Marketplace, väljer **AI och Cognitive Services** och identifiera listan över tillgängliga API: er. Klicka på **se alla** att se hela listan med Cognitive Services API: er. Klicka på API: et för att fortsätta.

    ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. På den **skapa** anger du följande information:

   - **Kontonamn:** namnet på kontot. Vi rekommenderar att du använder ett beskrivande namn, till exempel *AFaceAPIAccount*.

   - **Prenumeration:** väljer du något av de tillgängliga Azure-prenumerationer som du har deltagarbehörighet.

   - **API-typ:** väljer Cognitive Services-API som du vill använda. Mer information om olika Cognitive Services API: erna finns på [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) plats.

   - **Prisnivå:** kostnaden för Cognitive Services-kontot beror på den faktiska användningen och de alternativ du väljer. Mer information om priser för varje API i den [prissidor](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Resursgrupp:** en resursgrupp är en samling resurser som delar samma livscykel, behörigheter och principer. Mer information om resursgrupper finns [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Plats för resursgruppen:** detta krävs endast om API: et som valts är globala (inte bunden till en plats). Om API: et är global och inte knuten till en plats kan ange du dock en plats för resursgruppen där metadata som tillhör Cognitive Services API-konto finns. Den här platsen påverkar inte runtime tillgängligheten för ditt konto. Läs mer om resursgruppen i [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Explicit bekräftelse av villkoren för Online Service:** för att skapa ett konto, Prenumerationsägare eller deltagare (som definieras av [rollbaserad åtkomstkontroll i](https://docs.microsoft.com/azure/role-based-access-control/overview)) måste du uttryckligen är medveten om villkoren som gäller för Cognitive Services i [villkoren för Online Service](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Prenumerationen ägare kan inaktivera skapandet av Cognitive Services-konto för en specifik resursgrupp eller prenumeration via [Azure principer](../azure-policy/azure-policy-introduction.md) genom att följa artikeln [med hjälp av Azure portal för att tilldela och hantera resursprinciper](../azure-policy/assign-policy-definition.md) och tilldela en principdefinition för ”tillåts inte resurstyper” och ange **Microsoft.CognitiveServices/accounts** som resource måltypen.

     Om kontot skapas har inaktiverats, visas följande fel vid tidpunkten för har skapats:

     ![Fel när kontot skapades](media/cognitive-services-apis-create-account/error-message.png)

5. Om du vill fästa kontot instrumentpanelen för Azure portal, klickar du på **fäst på instrumentpanelen**.

6. Skapa kontot genom att klicka på **Skapa**.

När Cognitive Services-kontot har distribuerats klickar du på panelen i instrumentpanelen och visa informationen.

Du kan använda den **slutpunkts-URL** i den **översikt** avsnittet och nycklar i den **nycklar** avsnitt för att börja skapa API-anrop i dina program.

![Visa kontoinformation](media/cognitive-services-apis-create-account/display-account.png)

![Visa kontonycklar](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Nästa steg

Läs mer om alla Microsoft Cognitive Services tillgängliga [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

För snabbstartguider till med hjälp av några exempel Cognitive Services API: er:

 - [Snabbstart i datorn Vision C#](computer-vision/quickstarts/csharp.md)
 - [Textanalys med Python](text-analytics/quickstarts/python.md)
 - [Ansikts-API med JavaScript](face/quickstarts/javascript.md)
