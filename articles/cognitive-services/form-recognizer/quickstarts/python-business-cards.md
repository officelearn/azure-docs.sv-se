---
title: 'Snabb start: extrahera visitkorts data med hjälp av python-formulär igenkänning'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du formulär tolken REST API med python för att extrahera data från grafik kort på engelska.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 45e091fe1ed77a4efc90d426b1d9a2842ae00175
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725451"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Snabb start: extrahera visitkorts data med hjälp av formulär tolken REST API med python

I den här snabb starten använder du Azures formulär igenkännings REST API med python för att extrahera och identifiera relevant information om visitkort.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:
- [Python](https://www.python.org/downloads/) installerat (om du vill köra exemplet lokalt).
- En URL för en bild av ett visitkort. Du kan använda en [exempel bild](../media/business-card-english.jpg) för den här snabb starten.

> [!NOTE]
> Den här snabb starten använder en fjärran sluten visitkorts avbildning som används av URL. Om du vill använda lokala filer i stället, se [referens dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analysera ett visitkort

Om du vill börja analysera ett visitkort anropar du **[visitkorts](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** -API: et med python-skriptet nedan. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<Endpoint>` med den slut punkt som du fick med din igenkännings prenumeration för formulär.
1. Ersätt `<your business card URL>` med URL-adressen till en visitkorts bild.
1. Ersätt `<subscription key>` med den prenumerations nyckel som du kopierade från föregående steg.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
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

Du får ett `202 (Success)` svar som innehåller ett **Åtgärds plats** huvud som skriptet skriver ut till-konsolen. Den här rubriken innehåller ett åtgärds-ID som du kan använda för att fråga efter statusen för den asynkrona åtgärden och hämta resultatet. I följande exempel värde är strängen efter `operations/` Åtgärds-ID: t.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults{operationID}
```

## <a name="get-the-business-card-results"></a>Hämta resultat från företags kortet

När du har anropat API för **analys av visitkort** anropar du API: et för att **[analysera affärskorts resultat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** för att hämta status för åtgärden och de extraherade data. Lägg till följande kod längst ned i python-skriptet. Detta använder åtgärds-ID-värdet i ett nytt API-anrop. Det här skriptet anropar API: n med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund.

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

Skriptet kommer att skriva ut svar till konsolen tills åtgärden **analysera visitkort** slutförs. Sedan skrivs den extraherade text informationen in i JSON-format. `"recognitionResults"`Fältet innehåller alla rader med text som har extraherats från visitkortet och `"understandingResults"` fältet innehåller nyckel/värde-information för de mest relevanta delarna av visitkortet.

![Ett visitkort från contoso Company](../media/business-card-english.jpg)

`"recognitionResults"`Noden innehåller all den identifierade texten. Texten sorteras efter sida, sedan efter rad, sedan efter enskilda ord. `"understandingResults"`Noden innehåller de företagsspecifika värden som modellen identifierade. Här hittar du användbara nyckel/värde-par som skatt, totalt, handels adress och så vidare.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken REST API med python för att extrahera innehållet i ett visitkort. Sedan läser du referens dokumentationen för att utforska formulärets tolknings-API i större djup.

> [!div class="nextstepaction"]
> [REST API referens dokumentation](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
