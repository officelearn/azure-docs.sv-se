---
title: Så här migrerar du projektet till 3.0 API
titlesuffix: Azure Cognitive Services
description: 'Lär dig hur du migrerar Custom Vision-projekt från den tidigare versionen av API: et till 3.0 API.'
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044014"
---
# <a name="migrate-to-the-30-api"></a>Migrera till 3.0 API

Custom Vision har nu blivit allmänt tillgängliga och har genomgått en API-uppdatering.
Den här uppdateringen innehåller några nya funktioner och är dock några ändringar:

* Förutsägelse-API är nu dela i två beroende på vilken projekt.
* Exportalternativet Vision AI Developer Kit (VAIDK) måste du skapa ett projekt på ett visst sätt.
* Standard iterationer har tagits bort och ersatts med en publicera / ta bort en namngiven iteration.

Den här guiden visar hur du uppdaterar dina projekt du arbetar med den nya API-versionen. Se den [viktig](release-notes.md) för en fullständig lista över ändringarna.

## <a name="use-the-updated-prediction-api"></a>Använd den uppdaterade förutsägelse-API

2.x-API: er används samma förutsägelse anropet för både bildklassificerare och objekt detektor projekt. Båda projekttyperna av har godtagbar för den **PredictImage** och **PredictImageUrl** anrop. Från och med 3.0, har vi delat den här API: et så att du behöver att matcha projekttyp i anropet:

* Använd **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** och **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** att få förutsägelser för avbildning klassificering projekt.
* Använd **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** och **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** att få förutsägelser för objektet identifiering projekt.

## <a name="use-the-new-iteration-publishing-workflow"></a>Använda det nya iteration publishing arbetsflödet

2.x API: er används standard iteration eller en angiven iteration-ID för att välja iterationen ska användas för förutsägelse. Från och med 3.0, har vi antagit ett publicera flöde där du först publicera en iteration under ett visst angivet namn från API: et för utbildning. Du kan sedan skicka namnet till förutsägelse-metoder för att ange vilka iteration att använda.

> [!IMPORTANT]
> 3.0 API: er inte använder funktionen standard iteration. Tills vi avverka den äldre API: er kan du fortsätta att använda 2.x API: er för att växla en iteration som standard. Dessa API: er kommer att finnas kvar under en viss tidsperiod och du kan anropa den **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metod för att markera en iteration som standard.

### <a name="publish-an-iteration"></a>Publicera en iteration

När en iteration har tränats, du kan göra den tillgänglig för förutsägelse med hjälp av den **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metod. Om du vill publicera en iteration måste förutsägelse resurs-ID som är tillgänglig på webbplatsen CustomVision inställningssidan.

![Custom Vision webbplats inställningssidan med förutsägelse resurs-ID som beskrivs.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Du kan också få den här informationen från den [Azure-portalen](https://portal.azure.com) genom att gå till Custom Vision-förutsägelse resursen och välja **egenskaper**.

När din iteration har publicerats kan kan appar använda den för förutsägelse genom att ange namnet i sina förutsägande API-anrop. Om du vill göra en iteration otillgängligt för förutsägande anrop, använda den **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="additional-export-options"></a>Ytterligare exportalternativ

Med 3.0 API: er som vi visar två ytterligare exportera mål: ARM-arkitekturen och visuellt innehåll AI Developer Kit.

* Om du vill använda ARM, behöver du bara välja en kompakt domän och sedan välja DockerFile och sedan ARM som alternativ för export.
* För visuellt innehåll AI Dev Kit projektet måste skapas med den __allmänna (CD)__ domänen samt att ange VAIDK i målet exportera plattformar argumentet.

## <a name="next-steps"></a>Nästa steg

* [Referensdokumentation för utbildning-API (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Förutsägande API-referensdokumentation (REST)](https://go.microsoft.com/fwlink/?linkid=865445)