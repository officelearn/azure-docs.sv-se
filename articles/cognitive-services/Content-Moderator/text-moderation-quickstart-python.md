---
title: 'Snabbstart: Analysera textinnehåll i Python – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Så här analyserar du textinnehåll för olika stötande material med SDK för Content Moderator för Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 20920a04c5b85c9eede7d7b249dd6e0548308240
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883414"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Snabbstart: Analysera textinnehåll och leta efter stötande material i Python

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med Content Moderator SDK för Python. Du kommer lära dig att köra termbaserad filtrering och klassificering av innehåll i syfte att kontrollera potentiellt stötande material.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter
- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator och få din nyckel.
- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget

## <a name="get-the-content-moderator-sdk"></a>Hämta Content Moderator SDK

Installera Content Moderator Python SDK genom att öppna en kommandotolk och köra följande kommando:

```
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importera moduler

Skapa ett nytt Python-skript som heter _ContentModeratorQS.py_ och lägg till följande kod för att importera de nödvändiga delarna av SDK.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

Importera även funktionen kodformatering för att hantera det slutgiltiga resultatet.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>Initiera variabler

Lägg sedan till variabler för din prenumerationsnyckel till Content Moderator och slutpunktens webbadress. Du måste ersätta `<your subscription key>` med värdet för din nyckel. Du kanske även måste ändra värdet för `endpoint_url` för att använda den regionsidentifierare som motsvarar din prenumerationsnyckel. Nycklar för kostnadsfri utvärderingsprenumeration genereras i regionen **westus** (USA, västra).

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


För enkelhetens skull analyserar du text direkt från skriptet. Definiera en ny sträng med textinnehåll att ändra:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Fråga Moderator-tjänsten

Skapa instansen **ContentModeratorClient** med din prenumerationsnyckel och slutpunktens webbadress. Använd sedan dess medlemsinstans **TextModerationOperations** för att anropa API för moderering. Se referensdokumentationen **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** för mer information om hur du anropar den.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>Skriva ut svaret

Kontrollera slutligen att anropet har slutförts och att en **Skärm**-instans returnerades. Skriv sedan ut data som returnerades till konsolen.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


Exempeltexten som används i den här snabbstarten resulterar i följande utdata:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du har utvecklat ett enkelt Python-skript som använder Content Moderator-tjänsten för att hitta relevant information om ett angivet textexempel. Därefter kan du läsa mer om vad de olika flaggorna och klassificeringarna betyder så att du kan avgöra vilka data du behöver och hur din app ska hantera dem.

> [!div class="nextstepaction"]
> [Guide för textmoderering](text-moderation-api.md)
