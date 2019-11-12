---
title: 'Snabb start: asynkron syntes för lång Forms ljud (för hands version) – tal tjänst'
titleSuffix: Azure Cognitive Services
description: 'Använd den långa ljud-API: n för att konvertera text till tal asynkront och hämta ljud resultatet från en URI som tillhandahålls av tjänsten. Den här REST API är idealisk för innehålls leverantörer som behöver konvertera textfiler som är större än 10 000 tecken eller 50 stycken till syntetiskt tal.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d308623a323fecd39efa90639da71fb981936fe5
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930584"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Snabb start: asynkron syntes för lång Forms ljud i python (för hands version)

I den här snabb starten använder du den långa ljud-API: n för att konvertera text till tal asynkront och hämta ljud resultatet från en URI som tillhandahålls av tjänsten. Den här REST API är idealisk för innehålls leverantörer som behöver konvertera textfiler som är större än 10 000 tecken eller 50 stycken till syntetiskt tal. Mer information finns i avsnittet om [långa ljud-API](../../long-audio-api.md).

> [!NOTE]
> Asynkron syntes för lång Forms ljud kan bara användas med [anpassade neurala-röster](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* Python 2.7. x eller 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)eller din favorit text redigerare.
* En Azure-prenumeration och en prenumerations nyckel för en röst tjänst. [Skapa ett Azure-konto](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account) och [skapa en tal resurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) för att hämta nyckeln. När du skapar tal resursen måste du kontrol lera att pris nivån är inställd på **S0**och att platsen är inställd på en [region som stöds](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan kodfragmentet till en fil med namnet `voice_synthesis_client.py`.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Om du inte har använt de här modulerna behöver du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests urllib3`.

Dessa moduler används för att parsa argument, konstruera HTTP-begäran och anropa text-till-tal-lång ljud REST API.

## <a name="get-a-list-of-supported-voices"></a>Hämta en lista över röster som stöds

Den här koden hämtar en lista över tillgängliga röster som du kan använda för att konvertera text till tal. Lägg till den här koden `voice_synthesis_client.py`:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>Testa koden

Vi testar vad du har gjort hittills. Kör det här kommandot, Ersätt `<your_key>` med din tal prenumerations nyckel och `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Du bör få utdata som ser ut så här:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>Omvandla text till tal

Nästa steg är att förbereda en textfil för indata. Det kan vara antingen oformaterad text eller SSML, men det måste vara mer än 10 000 tecken eller 50 stycken. En fullständig lista över kraven finns i [långa ljud-API](../../long-audio-api.md).

När du har för berett text filen. Nästa steg är att lägga till kod för tal syntes i projektet. Lägg till den här koden i `voice_synthesis_client.py`:

> [!NOTE]
> Som standard är ljud uppspelningen inställd på riff-16khz-bitarsläge-mono-PCM. Mer information om ljud utdata som stöds finns i [långa ljud-API](../../long-audio-api.md#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Operation-Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>Testa koden

Nu ska vi prova att göra en begäran om att syntetisera text med hjälp av indatafilen som en källa. Du måste uppdatera några saker i begäran nedan:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänst. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<input>` med sökvägen till text filen som du vill konvertera från text till tal.
* Ersätt `<locale>` med önskat utmatnings språk. Mer information finns i [språk stöd](../../language-support.md#neural-voices).
* Ersätt `<voice_guid>` med den önskade rösten för ljud uppspelningen. Använd en av rösterna som returneras av [Hämta en lista över röster som stöds](#get-a-list-of-supported-voices) eller Använd listan över neurala-röster som finns i [språk stöd](../../language-support.md#neural-voices).

Konvertera text till tal med det här kommandot:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> ' concatenateResult ' är en valfri parameter, om den här parametern inte anges kommer utdata att tillhandahållas som flera wave-filer, en för varje rad.

Du bör få utdata som ser ut så här:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Det angivna resultatet innehåller inmatnings texten och de ljudutdata som genererats av tjänsten. Dessa hämtas som ett zip.

## <a name="remove-previous-requests"></a>Ta bort tidigare begär Anden

Det finns en gräns på 2 000-begäranden för varje prenumeration. Därför kommer det att finnas tillfällen då du måste ta bort tidigare skickade förfrågningar innan du kan göra nya. Om du inte tar bort befintliga förfrågningar får du ett fel meddelande när du överskrider 2 000.

Lägg till den här koden i `voice_synthesis_client.py`:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>Testa koden

Kör det här kommandot, Ersätt `<your_key>` med din tal prenumerations nyckel och `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

Då returneras en lista över syntheses som du har begärt. Du bör få utdata som ser ut så här:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Nu ska vi använda några av dessa värden för att ta bort/ta bort tidigare skickade förfrågningar. Kör det här kommandot, Ersätt `<your_key>` med din tal prenumerations nyckel och `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal). `<synthesis_id>` ska vara ett av de värden som returnerades i den tidigare begäran.

> [!NOTE]
> Begär Anden med statusen "körs"/"väntar" kan inte tas bort eller tas bort.

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Du bör få utdata som ser ut så här:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Hämta den fullständiga klienten

Hela `voice_synthesis_client.py` kan laddas ned på [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om API: et för långa ljud](../../long-audio-api.md)
