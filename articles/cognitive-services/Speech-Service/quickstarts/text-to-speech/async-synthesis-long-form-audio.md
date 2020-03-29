---
title: 'Snabbstart: Asynkron syntes för ljud i långformat (förhandsgranskning) - Taltjänst'
titleSuffix: Azure Cognitive Services
description: Använd API:et För långt ljud för att asynkront konvertera text till tal och hämta ljudutdata från en URI som tillhandahålls av tjänsten. Det här REST API:et är idealiskt för innehållsleverantörer som behöver konvertera textfiler som är större än 10 000 tecken eller 50 stycken till syntetiserat tal.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331084"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Snabbstart: Asynkron syntes för ljud i långformat i Python (förhandsgranskning)

I den här snabbstarten ska du använda API:et För långt ljud för att asynkront konvertera text till tal och hämta ljudutdata från en URI som tillhandahålls av tjänsten. Det här REST API:et är idealiskt för innehållsleverantörer som behöver syntetisera ljud från text som är större än 5 000 tecken (eller mer än 10 minuter långa). Mer information finns i [Long Audio API](../../long-audio-api.md).

> [!NOTE]
> Asynkron syntes för långformigt ljud kan endast användas med [Custom Neural Voices](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Krav

För den här snabbstarten krävs:

* Python 2.7.x eller 3.x.
* [Visual Studio,](https://visualstudio.microsoft.com/downloads/) [Visual Studio Code](https://code.visualstudio.com/download)eller din favorittextredigerare.
* En Azure-prenumeration och en prenumerationsnyckel för taltjänsten. [Skapa ett Azure-konto](../../get-started.md#new-resource) och [skapa en talresurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) för att hämta nyckeln. När du skapar talresursen kontrollerar du att prisnivån är inställd på **S0**och att platsen är inställd på en [region som stöds](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Skapa ett projekt och importera nödvändiga moduler

Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan kodavsnittet till en `voice_synthesis_client.py`fil med namnet .

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
> Om du inte har använt dessa moduler måste du installera dem innan du kör programmet. För att installera de här paketen kör du: `pip install requests urllib3`.

Dessa moduler används för att tolka argument, konstruera HTTP-begäran och anropa det långa rest-API:et för långa ljudljud.

## <a name="get-a-list-of-supported-voices"></a>Få en lista över röster som stöds

Den här koden får en lista över tillgängliga röster som du kan använda för att konvertera text till tal. Lägg till `voice_synthesis_client.py`koden i:

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

Låt oss testa vad du har gjort hittills. Du måste uppdatera några saker i begäran nedan:

* Ersätt `<your_key>` med prenumerationsnyckeln för Taltjänsten. Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där talresursen `eastus` skapades `westus`(till exempel: eller ). Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).

Kör följande kommando:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Du ser en utdata som ser ut så här:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Förbereda indatafiler

Förbered en indatatextfil. Det kan vara antingen oformaterad text eller SSML-text. Information om indatafilkraven finns i hur du [förbereder innehåll för syntes](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Konvertera text till tal

När du har förberett indatatextfilen lägger du till den här koden för talsyntes i: `voice_synthesis_client.py`

> [!NOTE]
> concatenateResult är en valfri parameter. Om den här parametern inte är inställd genereras ljudutgångarna per stycke. Du kan också sammanfoga ljuden till 1-utgång genom att ställa in parametern. Som standard är ljudutgången inställd på riff-16khz-16bit-mono-pcm. Mer information om ljudutgångar som stöds finns i [Ljudutdataformat](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

Låt oss göra en begäran om att syntetisera text med hjälp av indatafilen som källa. Du måste uppdatera några saker i begäran nedan:

* Ersätt `<your_key>` med prenumerationsnyckeln för Taltjänsten. Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där talresursen `eastus` skapades `westus`(till exempel: eller ). Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<input>` med sökvägen till den textfil som du har förberett för text-till-tal.
* Ersätt `<locale>` mot önskat utgångsmedgångsmed på vilket utgångsmedgångsmedgång. Mer information finns i [språkstöd](../../language-support.md#neural-voices).
* Ersätt `<voice_guid>` med önskad utdataröst. Använd en av de röster som returneras av [Hämta en lista med röster som stöds](#get-a-list-of-supported-voices).

Konvertera text till tal med det här kommandot:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Om du har fler än 1 indatafiler måste du skicka in flera begäranden. Det finns vissa begränsningar som måste vara medvetna. 
> * Klienten tillåts skicka upp till **5** begäranden till servern per sekund för varje Azure-prenumerationskonto. Om det överskrider begränsningen får klienten en felkod på 429 (för många begäranden). Minska begäran om belopp per sekund
> * Servern tillåts köra och köa upp till **120** begäranden för varje Azure-prenumerationskonto. Om den överskrider begränsningen returnerar servern en felkod på 429 (för många begäranden). Vänta och undvik att skicka in en ny begäran tills vissa begäranden har slutförts

Du ser en utdata som ser ut så här:

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

Resultatet innehåller indatatexten och ljudutdatafilerna som genereras av tjänsten. Du kan ladda ner dessa filer i en zip.

## <a name="remove-previous-requests"></a>Ta bort tidigare begäranden

Servern behåller upp till **20 000** begäranden för varje Azure-prenumerationskonto. Om beloppet för din begäran överskrider denna begränsning, ta bort tidigare begäranden innan du gör nya. Om du inte tar bort befintliga begäranden visas ett felmeddelande.

Lägg till `voice_synthesis_client.py`koden i:

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

Nu ska vi kontrollera vilka förfrågningar du har skickat tidigare. Innan du fortsätter måste du uppdatera några saker i den här begäran:

* Ersätt `<your_key>` med prenumerationsnyckeln för Taltjänsten. Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där talresursen `eastus` skapades `westus`(till exempel: eller ). Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).

Kör följande kommando:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Då returneras en lista över syntesbegäranden som du har gjort. Du ser en utdata så här:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Nu ska vi ta bort en tidigare skickad begäran. Du måste uppdatera några saker i koden nedan:

* Ersätt `<your_key>` med prenumerationsnyckeln för Taltjänsten. Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där talresursen `eastus` skapades `westus`(till exempel: eller ). Den här informationen är tillgänglig på fliken **Översikt** för din resurs i [Azure-portalen](https://aka.ms/azureportal).
* Ersätt `<synthesis_id>` med det värde som returnerades i föregående begäran.

> [!NOTE]
> Begäranden med statusen "Köra"/väntar" kan inte tas bort eller tas bort.

Kör följande kommando:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Du ser en utdata så här:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Skaffa hela klienten

Den `voice_synthesis_client.py` färdiga är tillgänglig för nedladdning på [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om Long Audio API](../../long-audio-api.md)
