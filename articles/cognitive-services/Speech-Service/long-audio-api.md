---
title: Tids krävande ljud-API (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: 'Lär dig hur den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 0a538deb3b7da19261e1bc2b7c0d29f35315f786
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015421"
---
# <a name="long-audio-api-preview"></a>Långt ljud-API (för hands version)

Den långa ljud-API: n är utformad för asynkron syntes av lång Forms text till tal (till exempel: ljud böcker, nyhets artiklar och dokument). Det här API: t returnerar inte syntetiskt ljud i real tid, i stället är det förväntat att du ska söka efter svar och använda de utdata som de görs tillgängliga från tjänsten. Till skillnad från text till tal-API som används av talet SDK, kan det långa ljud-API: et skapa syntetiskt ljud som är längre än 10 minuter, vilket gör det perfekt för utgivare och ljud innehålls plattformar.

Ytterligare fördelar med den långa ljud-API: et:

* Syntetiskt tal som returneras av tjänsten använder de bästa neurala-rösterna.
* Du behöver inte distribuera en röst slut punkt när den syntetiserar röster i ingen real tids batch-läge.

> [!NOTE]
> API för långa ljud stöder nu både [offentliga neurala-röster](./language-support.md#neural-voices) och [anpassade neurala-röster](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Arbetsflöde

När du använder det långa ljud-API: et skickar du normalt en textfil eller filer som ska syntetiseras, avsöker efter status. om statusen är klar kan du ladda ned ljud uppspelningen.

Det här diagrammet innehåller en översikt över arbets flödet.

![Arbets flödes diagram för långt ljud-API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Förbered innehåll för syntes

När du förbereder text filen måste du se till att den:

* Är antingen oformaterad text (. txt) eller SSML text (. txt)
* Kodas som [UTF-8 med byte ordnings tecken (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Är en enskild fil, inte ett zip
* Innehåller fler än 400 tecken för oformaterad text eller 400 [fakturerbara tecken](./text-to-speech.md#pricing-note) för SSML text och färre än 10 000 stycken
  * För oformaterad text separeras varje stycke genom att trycka på **RETUR/retur** -Visa [text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * För SSML text betraktas varje SSML-enhet som ett stycke. SSML bitar separeras av olika stycken – Visa [SSML text ingångs exempel](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> För kinesiska (fast), kinesiska (Hongkong SAR), kinesiska (Taiwan), japanska och koreanska, räknas ett ord som två tecken. 

## <a name="python-example"></a>Python-exempel

Det här avsnittet innehåller python-exempel som visar den grundläggande användningen av det långa ljud-API: et. Skapa ett nytt Python-projekt med valfri IDE eller redigeringsprogram. Kopiera sedan kodfragmentet till en fil med namnet `voice_synthesis_client.py` .

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

Dessa bibliotek används för att parsa argument, konstruera HTTP-begäran och anropa text-till-tal-lång ljud REST API.

### <a name="get-a-list-of-supported-voices"></a>Hämta en lista över röster som stöds

Med den här koden kan du få en fullständig lista över röster för en speciell region eller slut punkt som du kan använda. Lägg till koden i `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

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

Kör skriptet med kommandot `python voice_synthesis_client.py --voices -key <your_key> -region <region>` och Ersätt följande värden:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänsten. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus` ). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).

Du ser ett utdata som ser ut så här:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Om **PublicVoice** -parametern är **True** är rösten offentlig neurala röst. Annars är den anpassade neurala rösten.

### <a name="convert-text-to-speech"></a>Omvandla text till tal

Förbered en textfil, antingen i oformaterad text eller SSML text, och Lägg sedan till följande kod i `voice_synthesis_client.py` :

> [!NOTE]
> ' concatenateResult ' är en valfri parameter. Om den här parametern inte anges genereras ljud utmatningarna per stycke. Du kan också sammanfoga ljuden till 1 utdata genom att ange parametern. Som standard är ljud uppspelningen inställd på riff-16khz-bitarsläge-mono-PCM. Mer information om ljud utdata som stöds finns i [format för ljud uppspelning](#audio-output-formats).

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

Kör skriptet med kommandot `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` och Ersätt följande värden:

* Ersätt `<your_key>` med din prenumerations nyckel för röst tjänsten. Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<region>` med den region där din tal resurs skapades (till exempel: `eastus` eller `westus` ). Den här informationen finns på fliken **Översikt** för resursen i [Azure Portal](https://aka.ms/azureportal).
* Ersätt `<input>` med sökvägen till text filen som du har för berett för text till tal.
* Ersätt `<locale>` med det önskade utmatnings språket. Mer information finns i [språk stöd](language-support.md#neural-voices).
* Ersätt `<voice_guid>` med den önskade utdata-rösten. Använd en av de röster som returnerades av ditt föregående anrop till `/voicesynthesis/voices` slut punkten.

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

> [!NOTE]
> Om du har fler än 1 indatafiler måste du skicka flera begär Anden. Det finns vissa begränsningar som måste vara medvetna. 
> * Klienten får skicka upp till **5** förfrågningar till server per sekund för varje Azure-prenumerations konto. Om den överskrider begränsningen får klienten en 429-felkod (för många begär Anden). Minska antalet begär Anden per sekund
> * Servern kan köra och köa upp till **120** förfrågningar för varje Azure-prenumerations konto. Om den överskrider begränsningen returnerar servern en 429-felkod (för många begär Anden). Vänta och Undvik att skicka ny begäran förrän vissa begär Anden har slutförts

### <a name="remove-previous-requests"></a>Ta bort tidigare begär Anden

Tjänsten håller upp till **20 000** förfrågningar för varje Azure-prenumerations konto. Om ditt begär ande belopp överskrider den här begränsningen kan du ta bort tidigare begär Anden innan du gör nya. Om du inte tar bort befintliga förfrågningar får du ett fel meddelande.

Lägg till följande kod i `voice_synthesis_client.py`:

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

Kör `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` för att hämta en lista över syntes förfrågningar som du har gjort. Du ser utdata som liknar följande:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

För att ta bort en begäran, kör `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` och Ersätt `<synthesis_id>` med ett ID-värde för begäran som returnerades från föregående begäran.

> [!NOTE]
> Begär Anden med statusen "körs"/"väntar" kan inte tas bort eller tas bort.

Slutfört `voice_synthesis_client.py` är tillgängligt på [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>HTTP-statuskoder

I följande tabell beskrivs HTTP-svars koderna och meddelanden från REST API.

| API | HTTP-statuskod | Beskrivning | Lösning |
|-----|------------------|-------------|----------|
| Skapa | 400 | Röst syntesen är inte aktive rad i den här regionen. | Ändra tal prenumerations nyckeln med en region som stöds. |
|        | 400 | Endast **standard** tal prenumerationen för den här regionen är giltig. | Ändra tal prenumerations nyckeln till pris nivån "standard". |
|        | 400 | Överskrid gränsen för 20 000-begäran för Azure-kontot. Ta bort några förfrågningar innan du skickar nya. | Servern kommer att ha upp till 20 000 förfrågningar för varje Azure-konto. Ta bort några begär Anden innan du skickar nya. |
|        | 400 | Det går inte att använda den här modellen i röst syntesen: {modelID}. | Kontrol lera att status för {modelID} är korrekt. |
|        | 400 | Regionen för begäran matchar inte regionen för modellen {modelID}. | Kontrol lera att {modelID} s region stämmer överens med begärans region. |
|        | 400 | Röst syntesen stöder bara text filen i UTF-8-kodningen med byte-ordnings markören. | Kontrol lera att indatafilerna är i UTF-8-kodning med markör för byte-ordning. |
|        | 400 | Endast giltiga SSML-indata är tillåtna i röst syntes förfrågan. | Kontrol lera att SSML-uttrycken är korrekta. |
|        | 400 | Det gick inte att hitta röst namnet {voiceName} i indatafilen. | Röst namnet för SSML är inte justerat med modell-ID: t. |
|        | 400 | Antalet stycken i indatafilen ska vara mindre än 10 000. | Kontrol lera att antalet stycken i filen är mindre än 10 000. |
|        | 400 | Indatafilen får innehålla mer än 400 tecken. | Kontrol lera att indatafilen överskrider 400 tecken. |
|        | 404 | Det går inte att hitta modellen som har deklarerats i röst syntes definitionen: {modelID}. | Kontrol lera att {modelID} är korrekt. |
|        | 429 | Överskrida den aktiva röst syntes gränsen. Vänta tills några begär Anden har slutförts. | Servern kan köra och köa upp till 120 förfrågningar för varje Azure-konto. Vänta och Undvik att skicka nya begär Anden förrän vissa begär Anden har slutförts. |
| Alla       | 429 | Det finns för många begär Anden. | Klienten får skicka upp till 5 förfrågningar till server per sekund för varje Azure-konto. Minska antalet begär Anden per sekund. |
| Ta bort    | 400 | Röst syntes aktiviteten används fortfarande. | Du kan bara ta bort begär Anden som har **slutförts** eller **misslyckats**. |
| GetByID   | 404 | Det går inte att hitta den angivna entiteten. | Kontrol lera att syntes-ID: t är korrekt. |

## <a name="regions-and-endpoints"></a>Regioner och slut punkter

Den långa ljud-API: n är tillgänglig i flera regioner med unika slut punkter.

| Region | Slutpunkt |
|--------|----------|
| Australien, östra | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Kanada, centrala | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indien, centrala | `https://centralindia.customvoice.api.speech.microsoft.com` |
| USA, södra centrala | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Sydostasien | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Storbritannien, södra | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa, västra | `https://westeurope.customvoice.api.speech.microsoft.com` |
| USA, västra 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Format för ljud uppspelning

Vi har stöd för flexibla format för ljud uppspelning. Du kan generera ljud utmatningar per stycke eller sammanfoga ljud utmatningarna till en enda utmatning genom att ange parametern "concatenateResult". Följande format för ljudutdata stöds av den långa ljud-API: et:

> [!NOTE]
> Standard ljud formatet är riff-16khz-bitarsläge-mono-PCM.

* riff-8khz-bitarsläge-mono-PCM
* riff-16khz-bitarsläge-mono-PCM
* riff-24khz-bitarsläge-mono-PCM
* riff-48kHz-bitarsläge-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3

## <a name="sample-code"></a>Exempelkod
Exempel kod för långsiktigt ljud-API finns på GitHub.

* [Exempel kod: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exempel kod: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exempel kod: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)