---
title: 'Snabb start: Azure-hanterings klient bibliotek för python'
description: I den här snabb starten kommer du igång med Azures hanterings klient bibliotek för python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 743b05b38eddc80ce7462a3439613fc767d91daa
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607797"
---
[Referens dokumentation](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices)  |  [Paket (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="prerequisites"></a>Förutsättningar

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="create-an-azure-service-principal"></a>Skapa ett huvud namn för Azure-tjänsten

För att ditt program ska interagera med ditt Azure-konto behöver du ett Azure-tjänstens huvud namn för att hantera behörigheter. Följ instruktionerna i [skapa ett huvud namn för Azure-tjänsten](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0).

När du skapar ett huvud namn för tjänsten visas det ett hemligt värde, ett ID och ett program-ID. Spara program-ID och hemlighet till en tillfällig plats för senare steg.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Innan du skapar en Cognitive Services resurs måste ditt konto ha en Azure-resurs grupp som innehåller resursen. Om du inte redan har en resurs grupp skapar du en i [Azure Portal](https://ms.portal.azure.com/).

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE och navigera till ditt projekt i ett konsol fönster.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Du kan installera klient biblioteket med:

```console
pip install azure-mgmt-cognitiveservices
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

### <a name="import-libraries"></a>Importera bibliotek

Öppna python-skriptet och importera följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till följande fält i roten i skriptet och fyll i deras värden med hjälp av tjänstens huvud namn som du skapade och din Azure konto information.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Lägg sedan till följande kod för att skapa ett **CognitiveServicesManagementClient** -objekt. Det här objektet krävs för alla dina Azure-hanterings åtgärder.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

### <a name="choose-a-service-and-pricing-tier"></a>Välj en tjänst och en pris nivå

När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du använder denna och annan information som parametrar när du skapar resursen. Följande funktion visar en lista över tillgängliga kognitiva tjänst typer.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

Se listan över SKU: er och pris information nedan. 

#### <a name="multi-service"></a>Multi-service

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Flera tjänster. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Visuellt innehåll

| Tjänst                    | Variant                      |
|----------------------------|---------------------------|
| Visuellt innehåll            | `ComputerVision`          |
| Custom Vision förutsägelse | `CustomVision.Prediction` |
| Custom Vision-utbildning   | `CustomVision.Training`   |
| Ansikte                       | `Face`                    |
| Formigenkänning            | `FormRecognizer`          |
| Handskriftsigenkänning             | `InkRecognizer`           |

#### <a name="search"></a>Search

| Tjänst            | Variant                  |
|--------------------|-----------------------|
| Automatiska förslag i Bing   | `Bing.Autosuggest.v7` |
| Anpassad sökning i Bing | `Bing.CustomSearch`   |
| Entitetssökning i Bing | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Stavningskontroll i Bing   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Tjänst            | Variant                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Taligenkänning | `SpeakerRecognition` |

#### <a name="language"></a>Språk

| Tjänst            | Variant                |
|--------------------|---------------------|
| Formulär förståelse | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Textanalys     | `TextAnalytics`     |
| Textöversättning   | `TextTranslation`   |

#### <a name="decision"></a>Beslut

| Tjänst           | Variant               |
|-------------------|--------------------|
| Avvikelseidentifiering  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personanpassning      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Pris nivåer och fakturering

Pris nivåerna (och den mängd du debiteras) baseras på antalet transaktioner som du skickar med hjälp av autentiseringsinformationen. Varje pris nivå anger:
* maximalt antal tillåtna transaktioner per sekund (TPS).
* tjänst funktioner som Aktiver ATS inom pris nivån.
* kostnad för ett fördefinierat antal transaktioner. Om du fortsätter över det här numret kommer det att leda till en extra avgift som anges i [pris informationen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) för din tjänst.

> [!NOTE]
> Många av de Cognitive Services har en kostnads fri nivå som du kan använda för att testa tjänsten. Använd den kostnads fria nivån `F0` som SKU för din resurs.

## <a name="create-a-cognitive-services-resource"></a>Skapa en -resurs för Cognitive Services

Använd funktionen **skapa** för att skapa och prenumerera på en ny Cognitive Services-resurs. Den här funktionen lägger till en ny fakturerbar resurs i resurs gruppen som du skickar. När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats. Följande funktion tar alla dessa argument och skapar en resurs.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

## <a name="view-your-resources"></a>Visa dina resurser

Använd följande funktion om du vill visa alla resurser under ditt Azure-konto (i alla resurs grupper):

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Ta bort en resurs

Följande funktion tar bort den angivna resursen från den angivna resurs gruppen.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Funktioner för att anropa hantering

Lägg till följande kod längst ned i skriptet för att anropa ovanstående funktioner. Den här koden visar en lista över tillgängliga resurser, skapar en exempel resurs, listar dina ägda resurser och tar sedan bort exempel resursen.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet från kommando raden med `python` kommandot.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Se även

* [Dokumentation om Azure Management SDK-referens](https://docs.microsoft.com/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices?view=azure-python)
* [Vad är Azure Cognitive Services?](../../Welcome.md)
* [Autentisera begär anden till Azure Cognitive Services](../../authentication.md)
* [Skapa en ny resurs med Azure Portal](../../cognitive-services-apis-create-account.md)