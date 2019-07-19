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
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 01c153f2f8836b7d99de57af60b8623e54c6d6fe
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311928"
---
[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Snabbstart: Analysera textinnehåll och leta efter stötande material i Python

Den här artikeln innehåller information och kodexempel som hjälper dig att komma igång med Content Moderator SDK för Python. Du kommer lära dig att köra termbaserad filtrering och klassificering av innehåll i syfte att kontrollera potentiellt stötande material.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar
- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator och få din nyckel.
- [Python 2.7+ eller 3.5+](https://www.python.org/downloads/)
- [pip](https://pip.pypa.io/en/stable/installing/)-verktyget

## <a name="get-the-content-moderator-sdk"></a>Hämta Content Moderator SDK

Installera Content Moderator Python SDK genom att öppna en kommandotolk och köra följande kommando:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importera moduler

Skapa ett nytt Python-skript som heter _ContentModeratorQS.py_ och lägg till följande kod för att importera de nödvändiga delarna av SDK. Den kodformatering-modulen ingår för en enklare läsning av JSON-svar.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Initiera variabler

Lägg sedan till variabler för din prenumerationsnyckel till Content Moderator och slutpunktens webbadress. Du måste lägga till namnet `CONTENT_MODERATOR_SUBSCRIPTION_KEY` i miljövariablerna och lägga till din prenumerations nyckel som värde. För URL: en för bas slut `CONTENT_MODERATOR_ENDPOINT` punkten lägger du till till miljövariablerna med din landsspecifika URL som värde, till `https://westus.api.cognitive.microsoft.com`exempel. Nycklar för kostnadsfri utvärderingsprenumeration genereras i regionen **westus** (USA, västra).

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

En sträng med flerradig text från en fil kommer att bli kontrollerad. Inkludera filen [content_moderator_text_moderation. txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) i den lokala rotmappen och Lägg till dess fil namn i dina variabler:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Fråga Moderator-tjänsten

Skapa instansen **ContentModeratorClient** med din prenumerationsnyckel och slutpunktens webbadress. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Använd sedan klienten med dess medlems **TextModerationOperations** -instans för att anropa redigerings-API: t med funktionen `screen_text`. Se referensdokumentationen **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** för mer information om hur du anropar den.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Kontrol lera det utskrivna svaret

Kör exemplet och bekräfta svaret. Den bör ha slutförts och returnerade en **skärm** instans. Ett lyckat resultat skrivs ut nedan:

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
