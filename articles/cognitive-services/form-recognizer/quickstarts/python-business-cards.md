---
title: 'Snabb start: extrahera visitkorts data med hjälp av python-formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med python för att extrahera data från grafik kort på engelska.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 67a21dd86059f6cf1f017ce3eada285d2faab1e6
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012432"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Snabb start: extrahera visitkorts data med hjälp av formulär tolken REST API med python

I den här snabb starten använder du Azures formulär igenkännings REST API med python för att extrahera och identifiera relevant information om visitkort.

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En bild av ett visitkort. Du kan använda en [exempel bild](../media/business-card-english.jpg) för den här snabb starten.

> [!NOTE]
> Den här snabb starten använder en lokal fil. Om du vill använda en fjärran sluten visitkorts avbildning som används av URL i stället, se [referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync).

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analysera ett visitkort

Om du vill börja analysera ett visitkort anropar du **[visitkorts](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** -API: et med python-skriptet nedan. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<path to your business card>` med den lokala sökvägen till din visitkorts bild eller PDF.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.
1. Ersätt `<file type>` med "image/jpeg", "image/png", "Application/PDF" eller "image/TIFF".

    ```python
    ########### Python Form Recognizer Async Business Cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze"
    source = r"<path to your business card>"
    content_type = "<file type>"
    
    headers = {
        # Request headers
        'Content-Type': content_type,
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True  # True to output all recognized text
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-Recognizer-BusinessCards.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Exempelvis `python form-recognizer-businesscards.py`.

Du får ett `202 (Success)` svar som innehåller ett **Åtgärds plats** huvud som skriptet skriver ut till-konsolen. Den här rubriken innehåller ett resultat-ID som du kan använda för att fråga efter statusen för den tids krävande åtgärden och hämta resultatet. I följande exempel värde är strängen efter `operations/` resultat-ID.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Hämta resultat från företags kortet

När du har anropat API för **analys av visitkort** anropar du API: et för att **[analysera affärskorts resultat](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** för att hämta status för åtgärden och de extraherade data. Lägg till följande kod längst ned i python-skriptet. Detta använder resultat-ID-värdet i ett nytt API-anrop. Det här skriptet anropar API: n med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Spara skriptet.
1. Använd kommandot igen `python` för att köra exemplet. Exempelvis `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Granska svaret
![Ett visitkort från contoso Company](../media/business-card-english.jpg)

Det här exemplet illustrerar JSON-utdata som returneras av formulär tolken. Den har trunkerats för läsbarhet.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Skriptet kommer att skriva ut svar till konsolen tills åtgärden **analysera visitkort** slutförs. `"readResults"`Noden innehåller all den identifierade texten. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. `"documentResults"`Noden innehåller de företagsspecifika värden som modellen identifierade. Här hittar du användbar kontakt information, till exempel företagets namn, förnamn, efter namn, telefonnummer och så vidare.


## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med python för att extrahera innehållet i ett visitkort. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
