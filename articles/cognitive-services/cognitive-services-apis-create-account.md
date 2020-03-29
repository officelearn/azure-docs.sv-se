---
title: Skapa en Cognitive Services-resurs i Azure-portalen
titleSuffix: Azure Cognitive Services
description: Kom igång med Azure Cognitive Services genom att skapa och prenumerera på en resurs i Azure-portalen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219482"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Skapa en Cognitive Services-resurs med Azure-portalen

Använd den här snabbstarten för att börja använda Azure Cognitive Services. När du har skapat en Cognitive Service-resurs i Azure-portalen får du en slutpunkt och en nyckel för att autentisera dina program.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Krav

* En giltig Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Skapa en ny Azure Cognitive Services-resurs

1. skapa en resurs

    #### <a name="multi-service-resource"></a>[Resurs för flera tjänster](#tab/multiservice)
    
    Multi-service-resursen heter **Cognitive Services** i portalen. [Skapa en Cognitive Services-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    För närvarande ger multitjänstresursen åtkomst till följande Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Visuellt innehåll  | Content Moderator                                    | Ansikte               | Språkförståelse (LUIS) | Textanalys   |
    | Translator Text  | Bing Sök v7 <br>(Webb, Bild, Nyheter, Video, Visual) | Anpassad Bing-sökning | Entitetssökning i Bing            | Automatiska förslag i Bing |
    | Stavningskontroll i Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Resurs med en tjänst](#tab/singleservice)

    Använd länkarna nedan för att skapa en resurs för tillgängliga Cognitive Services:

    | Visuellt innehåll                      | Tal                  | Språk                          | Beslut             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Datorseende](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Uppslukande läsare](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Avvikelseidentifiering](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Sök API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Tjänst för anpassad syn](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Talarigenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Språk understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Anpassad Bing-sökning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Ansikte](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personanpassning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing entitetssökning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Handskriftsigenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Textanalys](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Stavningskontroll av Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Översättare Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Automatiska förslag i Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Ange följande information på sidan **Skapa:**

    #### <a name="multi-service-resource"></a>[Resurs för flera tjänster](#tab/multiservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din kognitiva tjänster resurs. Till exempel *MyCognitiveServicesResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Location** | Platsen för din kognitiva tjänstinstans. Olika platser kan införa svarstid, men har ingen inverkan på resursens körningstillgänglighet. |
    | **Prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [API-prisinformationen](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Resursgrupp** | Azure-resursgruppen som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärmen Skapa resurser](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Klicka på **Skapa**.

    #### <a name="single-service-resource"></a>[Resurs med en tjänst](#tab/singleservice)

    |    |    |
    |--|--|
    | **Namn** | Ett beskrivande namn för din kognitiva tjänster resurs. Till exempel *TextAnalyticsResource*. |
    | **Prenumeration** | Välj en av dina tillgängliga Azure-prenumerationer. |
    | **Location** | Platsen för din kognitiva tjänstinstans. Olika platser kan införa svarstid, men har ingen inverkan på resursens körningstillgänglighet. |
    | **Prisnivå** | Kostnaden för ditt Cognitive Services-konto beror på vilka alternativ du väljer och din användning. Mer information finns i [API-prisinformationen](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Resursgrupp** | Azure-resursgruppen som ska innehålla din Cognitive Services-resurs. Du kan skapa en ny grupp eller lägga till den i en befintlig grupp. |

    ![Skärmen Skapa resurser](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Klicka på **Skapa**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Hämta nycklarna till din resurs

1. När resursen har distribuerats klickar du på **Gå till resurs** under Nästa **steg**.

    ![Sök efter kognitiva tjänster](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Från snabbstartsfönstret som öppnas kan du komma åt nyckeln och slutpunkten.

    ![Hämta nyckel och slutpunkt](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services-prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som finns i gruppen bort.

1. I Azure-portalen expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer **Resursgrupper** för att visa listan över dina resursgrupper.
2. Leta reda på resursgruppen som innehåller den resurs som ska tas bort
3. Högerklicka på resursgruppslistan. Välj **Ta bort resursgrupp** och bekräfta.

## <a name="see-also"></a>Se även

* [Autentisera begäranden till Azure Cognitive Services](authentication.md)
* [Vad är Azure Cognitive Services?](Welcome.md)
* [Stöd för naturligt språk](language-support.md)
* [Stöd för Docker-behållare](cognitive-services-container-support.md)
