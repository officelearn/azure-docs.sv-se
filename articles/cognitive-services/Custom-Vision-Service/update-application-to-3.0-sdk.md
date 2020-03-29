---
title: Så här uppdaterar du projektet till 3.0-API:et
titleSuffix: Azure Cognitive Services
description: Lär dig hur du uppdaterar Custom Vision-projekt från den tidigare versionen av API:et till 3.0-API:et.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647507"
---
# <a name="update-to-the-30-api"></a>Uppdatera till 3.0-API:et

Custom Vision har nu nått allmän tillgänglighet och har genomgått en API-uppdatering.
Den här uppdateringen innehåller några nya funktioner och, viktigast av allt, några bryta ändringar:

* Förutsägelse-API:et är nu uppdelat i två baserat på projekttypen.
* Exportalternativet Vision AI Developer Kit (VAIDK) kräver att du skapar ett projekt på ett visst sätt.
* Standard iterationer har tagits bort till förmån för en publicera / avpublicera en namngiven iteration.

Den här guiden visar hur du uppdaterar dina projekt för att arbeta med den nya API-versionen. Se [viktig information](release-notes.md) för en fullständig lista över ändringarna.

## <a name="use-the-updated-prediction-api"></a>Använda det uppdaterade förutsägelse-API:et

2.x API:erna använde samma förutsägelseanrop för både bildklassificerare och objektdetektorprojekt. Båda projekttyperna var godtagbara för **predictImage-** och **PredictImageUrl-anropen.** Från och med 3.0 har vi delat upp det här API:et så att du måste matcha projekttypen med anropet:

* Använd **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** och **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** för att få förutsägelser för bildklassificeringsprojekt.
* Använd **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** och **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** för att få förutsägelser för objektidentifieringsprojekt.

## <a name="use-the-new-iteration-publishing-workflow"></a>Använda det nya arbetsflödet för iterationspublicering

2.x API:erna använde standarditerationen eller ett angivet iterations-ID för att välja den iteration som ska användas för förutsägelse. Från och med 3.0 har vi antagit ett publiceringsflöde där du först publicerar en iteration under ett angivet namn från utbildnings-API:et. Du skickar sedan namnet till förutsägelsemetoderna för att ange vilken iteration som ska användas.

> [!IMPORTANT]
> 3.0 API:erna använder inte standarditeringsfunktionen. Tills vi har inaktuella de äldre API:erna kan du fortsätta att använda 2.x-API:erna för att växla en iteration som standard. Dessa API:er kommer att underhållas under en viss tid och du kan anropa **[metoden UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** för att markera en iteration som standard.

### <a name="publish-an-iteration"></a>Publicera en iteration

När en iteration har tränats kan du göra den tillgänglig för förutsägelse med hjälp av **[metoden PublishIteration.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** Om du vill publicera en iteration behöver du förutsägelseresurs-ID:t, som är tillgängligt på CustomVision-webbplatsens inställningssida.

![Sidan Webbplatsinställningssida för anpassad vision med förutsägelseresurs-ID:et.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Du kan också hämta den här informationen från [Azure Portal](https://portal.azure.com) genom att gå till custom visionsprediktionsresursen och välja **Egenskaper**.

När din iteration har publicerats kan appar använda den för förutsägelse genom att ange namnet i sitt API-anrop för förutsägelse. Om du vill göra en iteration otillgänglig för förutsägelseanrop använder du **[API:et för avpublicering.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>Nästa steg

* [Dokumentation för API-referens för utbildning (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Prediktion API-referensdokumentation (REST)](https://go.microsoft.com/fwlink/?linkid=865445)