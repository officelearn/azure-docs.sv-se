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
ms.openlocfilehash: eef9a99e4c94fa45e21abfc9d19fcef1230ffe76
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944683"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Snabb start: asynkron syntes för lång Forms ljud i python (för hands version)

I den här snabb starten använder du den långa ljud-API: n för att konvertera text till tal asynkront och hämta ljud resultatet från en URI som tillhandahålls av tjänsten. Den här REST API är idealisk för innehålls leverantörer som behöver syntetisera ljud från text som är större än 5 000 tecken (eller mer än 10 minuter långt). Mer information finns i avsnittet om [långa ljud-API](../../long-audio-api.md).

> [!NOTE]
> Asynkron syntes för lång Forms ljud kan bara användas med [anpassade neurala-röster](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* Python 2.7. x eller 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)eller din favorit text redigerare.
* En Azure-prenumeration och en prenumerations nyckel för en röst tjänst. [Skapa ett Azure-konto](../../get-started.md#try-the-speech-service-using-a-new-azure-account) och [skapa en tal resurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) för att hämta nyckeln. När du skapar tal resursen måste du kontrol lera att pris nivån är inställd på **S0**och att platsen är inställd på en [region som stöds](../../regions.md#standard-and-neural-voices).

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
> Om du inte har använt de här modulerna måste du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests urllib3`.

Dessa moduler används för att parsa argument, konstruera HTTP-begäran och anropa text-till-tal-lång ljud REST API.

## <a name="get-a-list-of-supported-voices"></a>Hämta en lista över röster som stöds

Den här koden hämtar en lista över tillgängliga röster som du kan använda för att konvertera text till tal. Lägg till koden i `voice_synthesis_client.py`:

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

Vi testar vad du har gjort hittills. Du måste uppdatera några saker i begäran nedan:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänst. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

Kör följande kommando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Du ser ett utdata som ser ut så här:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Förbered indatafiler

Förbered en textfil för indata. Det kan vara antingen oformaterad text eller SSML text. Information om krav för indatafiler finns i så här [förbereder du innehåll för syntes](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Omvandla text till tal

När du har bearbetat in text filen lägger du till den här koden för tal syntes till `voice_synthesis_client.py`:

> [!NOTE]
> ' concatenateResult ' är en valfri parameter. Om den här parametern inte anges genereras ljud utmatningarna per stycke. Du kan också sammanfoga ljuden till 1 utdata genom att ange parametern. Som standard är ljud uppspelningen inställd på riff-16khz-bitarsläge-mono-PCM. Mer information om ljud utdata som stöds finns i [format för ljud uppspelning](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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
        location = response.headers['Location']
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

Vi gör en begäran om att syntetisera text med hjälp av indatafilen som källa. Du måste uppdatera några saker i begäran nedan:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänst. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<input>` med sökvägen till text filen som du har för berett för text till tal.
* Ersätt `<locale>` med önskat utmatnings språk. Mer information finns i [språk stöd](../../language-support.md#neural-voices).
* Ersätt `<voice_guid>` med önskad utdata-röst. Använd en av de röster som returneras av [Hämta en lista över röster som stöds](#get-a-list-of-supported-voices).

Konvertera text till tal med det här kommandot:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Om du har fler än 1 indatafiler måste du skicka flera begär Anden. Det finns vissa begränsningar som måste vara medvetna. 
> * Klienten får skicka upp till **5** förfrågningar till server per sekund för varje Azure-prenumerations konto. Om den överskrider begränsningen får klienten en 429-felkod (för många begär Anden). Minska antalet begär Anden per sekund
> * Servern kan köra och köa upp till **120** förfrågningar för varje Azure-prenumerations konto. Om den överskrider begränsningen returnerar servern en 429-felkod (för många begär Anden). Vänta och Undvik att skicka ny begäran förrän vissa begär Anden har slutförts

Du ser ett utdata som ser ut så här:

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

Resultatet innehåller inmatnings texten och de ljudutdata som genereras av tjänsten. Du kan ladda ned dessa filer i ett zip.

## <a name="remove-previous-requests"></a>Ta bort tidigare begär Anden

Servern kommer att behålla upp till **20 000** förfrågningar för varje Azure-prenumerations konto. Om ditt begär ande belopp överskrider den här begränsningen kan du ta bort tidigare begär Anden innan du gör nya. Om du inte tar bort befintliga förfrågningar får du ett fel meddelande.

Lägg till koden i `voice_synthesis_client.py`:

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

Nu ska vi kontrol lera vilka begär Anden som du har skickat. Innan du fortsätter måste du uppdatera några saker i denna begäran:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänst. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

Kör följande kommando:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Detta returnerar en lista över syntes förfrågningar som du har gjort. Du ser utdata som liknar följande:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Nu ska vi ta bort en tidigare skickad begäran. Du måste uppdatera några saker i koden nedan:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänst. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus`). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<synthesis_id>` med det värde som returnerades i föregående begäran.

> [!NOTE]
> Begär Anden med statusen "körs"/"väntar" kan inte tas bort eller tas bort.

Kör följande kommando:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Du ser utdata som liknar följande:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Hämta den fullständiga klienten

Den slutförda `voice_synthesis_client.py` kan laddas ned på [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om API: et för långa ljud](../../long-audio-api.md)
