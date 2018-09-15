---
title: 'Snabbstart: Skapa ett Cognitive Services-konto i Azure portal'
titleSuffix: Microsoft Docs
description: Lär dig hur du skapar ett konto för att komma åt Azure Cognitive Services.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: aahi
ms.reviewer: gibattag
ms.openlocfilehash: 232e0aa64eef4f6829813efff6e0abffd0a78518
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45605138"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Snabbstart: Skapa ett Cognitive Services-konto i Azure portal

Använd den här snabbstarten för att börja använda Azure Cognitive Services. De här tjänsterna representeras av Azure [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör det möjligt att ansluta till en eller flera av många Cognitive Services API: erna.

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration. Du kan [skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Skapa och prenumerera på en Azure Cognitive Services-resurs

1. Logga in på den [Azure-portalen](http://portal.azure.com), och klicka på **+ skapa en resurs**.
    
    ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Under Azure Marketplace, väljer **AI + Maskininlärning**. Om du inte ser den tjänst som du är intresserad av, klickar du på **se alla** att visa hela katalogen med Cognitive Services API: er.

    ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. På den **skapa** anger du följande information:

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för cognitive services. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyNameFaceAPIAccount*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Plats** | Platsen för din cognitive service-instans. Olika platser kan introducera svarstid, men har ingen inverkan på runtime tillgängligheten för din resurs. |
    | **prisnivå** | Kostnaden för Cognitive Services-kontot beror på de alternativ du väljer och din användning. Mer information finns i API: et [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Resursgrupp** | Den [Azure-resursgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) som innehåller din resurs för Cognitive Services. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Resursen skapa-skärmen](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Komma åt din resurs 

När du har skapat din resurs kommer du åt den från instrumentpanelen för Azure om du har Fäst. I annat fall hittar du den i **resursgrupper**.

Inom resurs för Cognitive Services kan du använda slutpunkts-URL och nycklar i den **översikt** avsnitt för att börja skapa API-anrop i dina program.

![Skärmen](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Datorn Vision API C#-självstudie](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Se också

* [Snabbstart: Extrahera handskriven text från en avbildning](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Självstudier: Skapa en app för att upptäcka och RAM ansikten i en bild](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Skapa en anpassad sökning-webbsida](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrera Språkförståelse (LUIS) med en bot med Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
