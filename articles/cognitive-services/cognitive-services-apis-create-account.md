---
title: 'Skapa ett konto för kognitiva Services API: er i Azure portal | Microsoft Docs'
description: 'Så här skapar du ett konto med Microsoft kognitiva Services API: er i Azure-portalen.'
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
ms.openlocfilehash: 3697dd0628f0028cb486139e92c032f0d757c6ed
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352695"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Skapa ett konto för kognitiva Services API: er i Azure-portalen

Om du vill använda Microsoft kognitiva Service API: er, måste du först skapa ett konto i Azure-portalen.

1. Logga in på [Azure-portalen](http://portal.azure.com).

2. Klicka på **+ skapa en resurs för**.

3. Välj under Azure Marketplace **AI + kognitiva tjänster** och identifiera listan över tillgängliga API: er. Klicka på **se alla** att se hela listan över kognitiva Services API: er. Klicka på API du väljer att fortsätta.

    ![Välj kognitiva Services API: er](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. På den **skapa** anger du följande information:

   - **Kontonamn:** namnet på kontot. Vi rekommenderar att du använder ett beskrivande namn, till exempel *AFaceAPIAccount*.

   - **Prenumerationen:** väljer du något av de tillgängliga Azure-prenumerationer som du har behörighet för deltagare.

   - **Typ av API:** Välj kognitiva Services API som du vill använda. Mer information om de olika kognitiva tjänster tillgängliga API: er finns i [kognitiva Services](https://azure.microsoft.com/services/cognitive-services/) plats.

   - **Prisnivån:** kostnaden för kognitiva Services-konto beror på den faktiska användningen och de alternativ du väljer. Mer information om priser för varje API som avser den [priser sidor](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Resursgrupp:** en resursgrupp är en samling resurser som delar samma livscykel, behörigheter och principer. Mer information om resursgrupper finns [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Resursgruppsplats:** detta krävs endast om det valda API: T är globala (inte kopplad till en plats). Om API: et är global och inte knuten till en plats, men måste du ange en plats för resursgruppen där metadata som associeras med kognitiva Services API-kontot finns. Den här platsen påverkar inte runtime tillgängligheten för ditt konto. Mer information om resursgruppen finns [hantera Azure-resurser via portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Explicit bekräftelse av Online Tjänstvillkor:** för att skapa ett konto och prenumeration ägare eller deltagare (som definieras av [rollbaserad åtkomstkontroll](https://docs.microsoft.com/azure/role-based-access-control/overview)) måste du uttryckligen accepterar villkoren som tillämpa kognitiva tjänster i [Online Tjänstvillkor](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Prenumerationens ägare kan inaktivera skapandet av kognitiva Services-konto för en viss resursgrupp eller prenumeration via [Azure principer](../azure-policy/azure-policy-introduction.md) genom att följa artikeln [med hjälp av Azure-portalen för att tilldela och hantera resursprinciper](../azure-policy/assign-policy-definition.md) och tilldela en principdefinition ”otillåtet resurstyper” och ange **Microsoft.CognitiveServices/accounts** som mål.

     Om kontoskapande av har inaktiverats visas felmeddelande vid tidpunkten för konto skapas:

     ![Fel vid skapande av konto](media/cognitive-services-apis-create-account/error-message.png)

5. Om du vill fästa kontot på Azure portal instrumentpanelen, klickar du på **fäst på instrumentpanelen**.

6. Skapa kontot genom att klicka på **Skapa**.

Klicka på panelen i instrumentpanelen för att visa kontoinformation om när kognitiva Services-konto har distribuerats.

Du kan använda den **slutpunkts-URL** i den **översikt** avsnitt och nycklar i den **nycklar** avsnittet att API-anrop i dina program.

![Visa kontoinformation](media/cognitive-services-apis-create-account/display-account.png)

![Visa nycklar](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Nästa steg

Mer information om alla Microsoft kognitiva tjänster som är tillgängliga finns [kognitiva Services](https://azure.microsoft.com/services/cognitive-services/).

För Snabbstart guider med några exempel kognitiva Services API: er:

 - [Datorn Vision C# Snabbstart](/computer-vision/quickstarts/csharp.md)
 - [Textanalys med Python](/text-analytics/quickstarts/python.md)
 - [Ansikts-API med JavaScript](/face/quickstarts/javascript.md)
