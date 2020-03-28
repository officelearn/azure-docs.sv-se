---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75446420"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analysera formulär för nyckelvärdespar och tabeller

Därefter ska du använda den nyutbildade modellen för att analysera ett dokument och extrahera nyckelvärdespar och tabeller från den. Anropa **[Api:et analysera formulär](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** genom att köra följande kod i ett nytt Python-skript. Innan du kör skriptet gör du följande ändringar:

1. Ersätt `<file path>` med formulärets filsökväg (till exempel C:\temp\file.pdf). Detta kan också vara URL:en till en fjärrfil. För den här snabbstarten kan du använda filerna under mappen **Testa** i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Ersätt `<model_id>` med det modell-ID som du fick i föregående avsnitt.
1. Ersätt `<endpoint>` med slutpunkten som du fick med prenumerationsnyckeln För formulärmedkänningsnyckel. Du hittar den på fliken **Resursöversikt för** formulärdeform.
1. Ersätt `<file type>` med filtypen. Typer som `application/pdf` `image/jpeg`stöds: , , `image/png`, `image/tiff`.
1. Ersätt `<subscription key>` med din prenumerationsnyckel.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
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

1. Spara koden i en fil med ett py-tillägg. Till exempel *form-recognizer-analyze.py*.
1. Öppna ett kommandotolksfönster.
1. I kommandotolken kör du exemplet med kommandot `python`. Till exempel `python form-recognizer-analyze.py`.

När du anropar **API:et** för `201 (Success)` analysera formulär får du ett svar med ett **åtgärdsplatshuvud.** Värdet för det här huvudet är ett ID som du ska använda för att spåra resultatet av analysåtgärden. Skriptet ovan skriver ut värdet för det här huvudet till konsolen.

## <a name="get-the-analyze-results"></a>Få analysresultaten

Lägg till följande kod längst ned i Python-skriptet. Detta använder ID-värdet från föregående anrop i ett nytt API-anrop för att hämta analysresultaten. Åtgärden **Analysera formulär** är asynkron, så det här skriptet anropar API:et med jämna mellanrum tills resultaten är tillgängliga. Vi rekommenderar ett intervall på en sekund eller mer.

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
