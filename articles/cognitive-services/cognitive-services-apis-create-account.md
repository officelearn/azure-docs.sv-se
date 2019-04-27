---
title: Skapa ett Cognitive Services-konto i Azure portal
titlesuffix: Azure Cognitive Services
description: 'Så här skapar du ett konto för Azure Cognitive Services API: er i Azure-portalen.'
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 6950cba5ac958233e7ea77c8dc783ca86cc5a386
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829505"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Snabbstart: Skapa ett Cognitive Services-konto i Azure portal

I den här snabbstarten lär du dig att registrera dig för Azure Cognitive Services och skapa en prenumeration för en tjänst eller flera tjänster. De här tjänsterna representeras av Azure [resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), vilket gör det möjligt att ansluta till en eller fler av Azure-API: er för Cognitive Services.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En giltig Azure-prenumeration. [Skapa ett konto](https://azure.microsoft.com/free/) utan kostnad.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Skapa och prenumerera på en Azure Cognitive Services-resurs

Innan du börjar är det viktigt att veta att det finns två typer av Azure Cognitive Services-prenumerationer. Först är en prenumeration på en enda tjänst, till exempel visuellt innehåll eller Speech Services. En prenumeration för en tjänst är begränsad till den här resursen. Andra är en prenumeration som har flera tjänster för Azure Cognitive Services. Den här prenumerationen kan du använda en enda prenumeration för de flesta av Azure Cognitive Services. Det här alternativet samlas även fakturering. Se [priser för Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) för ytterligare information.

>[!WARNING]
> De här tjänsterna för närvarande **inte** stöd för flera tjänster nycklar: QnA Maker Speech Services, Custom Vision och Avvikelseidentifiering detektor.

I nästa avsnitt vägleder dig genom att skapa en enda eller flera tjänster prenumeration.


### <a name="multi-service-subscription"></a>Flera tjänster prenumeration

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **+ skapa en resurs**.

    ![Välj API: er för Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Leta upp sökfältet och ange: **Cognitive Services**.

    ![Sök efter Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Välj **Cognitive Services**.

    ![Välj Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. På den **skapa** anger du följande information:

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din resurs för cognitive services. Vi rekommenderar att du använder ett beskrivande namn, till exempel *MyCognitiveServicesAccount*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Plats** | Platsen för din cognitive service-instans. Olika platser kan introducera svarstid, men har ingen inverkan på runtime tillgängligheten för din resurs. |
    | **prisnivå** | Kostnaden för Cognitive Services-kontot beror på de alternativ du väljer och din användning. Mer information finns i API: et [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Resursgrupp** | Den [Azure-resursgrupp](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) som innehåller din resurs för Cognitive Services. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Resursen skapa-skärmen](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>En tjänst-prenumeration

1. Logga in på den [Azure-portalen](https://portal.azure.com), och klicka på **+ skapa en resurs**.

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

> [!NOTE]
> Prenumerationsägare kan inaktivera skapandet av Cognitive Services-konton för resursgrupper och prenumerationer genom att använda [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), att tilldela en principdefinition för ”tillåts inte resurstyper” och ange **Microsoft.CognitiveServices/accounts** som resource måltypen.

När du har skapat din resurs kommer du åt den från instrumentpanelen för Azure om du har Fäst. I annat fall hittar du den i **resursgrupper**.

Inom resurs för Cognitive Services kan du använda slutpunkts-URL och nycklar i den **översikt** avsnitt för att börja skapa API-anrop i dina program.

![Skärmen](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Autentisera begäranden till Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>Se också

* [Snabbstart: Extrahera handskriven text från en avbildning](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Självstudie: Skapa en app för att upptäcka och RAM ansikten i en bild](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Skapa en anpassad sökning-webbsida](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrera Språkförståelse (LUIS) med en bot med Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
