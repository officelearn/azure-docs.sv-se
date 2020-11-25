---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 6a6b0d9740d19270f8daa3608bc125edd0fbec37
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005102"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analysera formulär för nyckel/värde-par och tabeller

Sedan använder du din nya tränade modell för att analysera ett dokument och extrahera nyckel/värde-par och tabeller från det. Anropa **[analys formulärets](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** API genom att köra följande kod i ett nytt Python-skript. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<file path>` med fil Sök vägen för ditt formulär (till exempel C:\temp\file.pdf). Detta kan också vara webb adressen till en fjärrfil. I den här snabb starten kan du använda filerna under mappen **test** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*).
1. Ersätt `<model_id>` med modell-ID: t som du fick i föregående avsnitt.
1. Ersätt `<endpoint>` med den slut punkt som du fick med ditt formulärs igenkännings prenumerations nyckel. Du hittar det på fliken **Översikt** i formulärets tolknings resurs.
1. Ersätt `<file type>` med filtypen. Typer som stöds: `application/pdf` , `image/jpeg` , `image/png` , `image/tiff` .
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

    # <a name="v20"></a>[v2.0](#tab/v2-0)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    
    # <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)
    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```    


    ---



1. Spara koden i en fil med fil namns tillägget. py. Till exempel *form-Recognizer-Analyze.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Exempelvis `python form-recognizer-analyze.py`.

När du anropar API: t **analysera formulär** får du ett `201 (Success)` svar med en **Åtgärds plats** rubrik. Värdet för den här rubriken är ett ID som du kommer att använda för att spåra resultatet av analys åtgärden. Skriptet ovan skriver ut värdet för den här rubriken till-konsolen.

## <a name="get-the-analyze-results"></a>Hämta analys resultatet

Lägg till följande kod längst ned i python-skriptet. Detta använder ID-värdet från det föregående anropet i ett nytt API-anrop för att hämta analys resultatet. Åtgärden **analysera formulär** är asynkron, så det här skriptet anropar API: n med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
