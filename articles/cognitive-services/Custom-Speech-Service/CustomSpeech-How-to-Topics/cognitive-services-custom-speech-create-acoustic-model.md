---
title: Självstudie om att skapa en akustisk modell med Custom Speech Service – Microsoft Cognitive Services | Microsoft Docs
description: I den här självstudien lär du dig att skapa en akustisk modell med Custom Speech Service i Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ms.openlocfilehash: 53e93a08782ba66e69b903c32c4c3c7417e5a801
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344582"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Självstudie: Skapa en anpassad akustisk modell

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

I den här självstudien skapar du en anpassad akustisk modell för taldata som du förväntar dig att appen ska känna igen. Att skapa en anpassad akustisk modell är användbart om appen har utformats för att användas i en viss miljö, till exempel en bullrig fabrik eller av en viss typ av användare.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Förbereda data
> * Importera mängden med akustiska data
> * Skapa den anpassade akustiska modellen

Om du inte har ett Cognitive Services-konto kan du skapa ett [kostnadsfritt konto](https://cris.ai) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att ditt Cognitive Services-konto är anslutet till en prenumeration genom att öppna sidan [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Cognitive Services-prenumerationer).

Om det inte finns några prenumerationer kan Cognitive Services skapa ett konto åt dig om du klickar på knappen **Get free subscription** (Skaffa kostnadsfri prenumeration). Alternativt kan du ansluta till en Custom Search Service-prenumeration som skapats i Azure-portalen genom att klicka på knappen **Connect existing subscription** (Anslut befintlig prenumeration).

Information om hur du skapar en Custom Search Service-prenumeration i Azure-portalen finns på sidan om att [skapa ett Cognitive Services APIs-konto i Azure-portalen](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Förbereda data

För att anpassa den akustiska modellen för en viss domän krävs en samling taldata. Den här samlingen består av en uppsättning ljudfiler med taldata och en textfil med transkriptioner av varje ljudfil. Ljuddata ska vara representativa för det scenario där du vill använda tolken.

Exempel:

*   Om du vill känna igen tal bättre i en fabriksmiljö med mycket oljud bör ljudfilen bestå av personer som talar i en högljudd fabrik.
<a name="Preparing data to customize the acoustic model"></a>
*   Om du är intresserad av att optimera prestanda för en enda talare, till exempel om vill transkribera alla Franklin Roosevelts Fireside Chats-tal, bör ljudfilerna bestå av många exempel på endast den talaren.

En akustisk datamängd för att anpassa den akustiska modellen består av två delar: (1) en uppsättning ljudfiler som innehåller taldata och (2) en fil som innehåller transkriptioner av alla ljudfiler.

### <a name="audio-data-recommendations"></a>Rekommendationer för ljuddata

*   Alla ljudfiler i datamängden bör lagras i ljudformatet WAV (RIFF).
*   Ljudet måste ha en samplingsfrekvens på 8 kHz eller 16 kHz, och exempelvärdena ska lagras som okomprimerade PCM 16-bitars heltal med tecken (korta).
*   Endast ljudfiler med en kanal (mono) stöds.
*   Ljudfilerna måste vara mellan 100 ms och 1 minut långa. Varje ljudfil ska helst börja och sluta med minst 100 ms tystnad; mellan 500 ms och 1 sekund är vanligt.
*   Om du har bakgrundsljud i dina data är det bra om du även har några exempel med längre tystnadsperioder i dina data, till exempel ett par sekunder före och/eller efter det talade innehållet.
*   Varje ljudfil bör bestå av ett enda uttryck, till exempel en enskild mening för diktering, en enskild fråga eller omgång av en dialog.
*   Varje ljudfil i datamängden ska ha ett unikt filnamn och filnamnstillägget ”wav”.
*   Uppsättningen ljudfiler ska placeras i en enskild mapp utan underkataloger, och hela uppsättningen ljudfiler ska packas ned som ett enda ZIP-filarkiv.

> [!NOTE]
> Dataimporter via webbportalen är för närvarande begränsade till 2 GB, vilket är den maximala storleken för en akustisk datamängd. Detta motsvarar cirka 17 timmar ljud som spelats in med 16 kHz eller 34 timmars ljudinspelningar på 8 kHz. De grundläggande kraven för ljuddata sammanfattas i följande tabell.
>

| Egenskap | Värde |
|---------- |----------|
| Filformat | RIFF (WAV) |
| Samplingsfrekvens | 8 000 eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Samplingsformat | PCM, 16-bitars heltal |
| Filens varaktighet | 0,1 sekunder < varaktighet < 60 sekunder |
| Tystnadsmarginal | > 0,1 sekunder |
| Arkivformat | Zip |
| Maximal arkivstorlek | 2 GB |

### <a name="transcriptions"></a>Transkriptioner

Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska ha namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

  Exempel:
```
  speech01.wav  speech recognition is awesome

  speech02.wav  the quick brown fox jumped all over the place

  speech03.wav  the lazy dog was not amused
```

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data laddas upp till Custom Speech Service. Läs avsnittet om [riktlinjer för transkription](cognitive-services-custom-speech-transcription-guidelines.md) för tillämpligt språk när du förbereder dina transkriptioner.

Följande steg utförs med hjälp av [Custom Speech Service Portal](https://cris.ai). 

## <a name="import-the-acoustic-data-set"></a>Importera mängden med akustiska data

När ljudfilerna och transkriptionerna har förberetts är de redo att importeras till tjänstwebbportalen.

För att göra det ser du först till att du är inloggad i [Custom Speech Service Portal](https://cris.ai). Klicka sedan på listrutan ”Custom Speech” (Anpassat tal) på det översta menyfliksområdet och välj ”Adaptation Data” (Anpassningsdata). Om det här är första gången du laddar upp data till Custom Speech Service visas en tom tabell med namnet ”Datasets” (Datamängder). 

Klicka på knappen ”Import” (Importera) på raden ”Acoustic Datasets” (Akustiska datamängder) så visar webbplatsen en sida för att ladda upp en ny datamängd.

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-import.png)

Ange ett _Name_ (Namn) och en _Description_ (Beskrivning) i motsvarande textrutor. Dessa är användbara för att hålla reda på de olika datamängder du laddar upp. Sedan klickar du på ”Choose File” (Välj fil) för ”Transcription File” (Transkriptionsfilen) och ”WAV files” (WAV-filer) och väljer transkriptionsfilen med oformaterad text respektive zip-arkivet med WAV-filer. När det här klart klickar du på ”Import” (Importera) för att ladda upp data. Sedan överförs dina data. Det kan ta flera minuter för större datamängder.

När uppladdningen är klar kommer du tillbaka till tabellen ”Acoustic Datasets” (Akustiska datamängder), och en post som motsvarar din akustiska datamängd visas. Observera att den har tilldelats ett unikt ID (GUID). Data får även en status som anger det aktuella tillståndet. Statusen för data blir ”Waiting” (Väntar) när de har placerats i kö för bearbetning, ”Processing” (Bearbetar) medan de verifieras och ”Complete” (Klar) när de är redo att användas.

Dataverifieringen innehåller en rad kontroller av ljudfilerna för att verifiera filformat, längd och samplingsfrekvens samt kontroller av transkriptionsfilerna för att verifiera filformatet och utföra viss textnormalisering.

När statusen är ”Complete” (Klart) kan du klicka på ”Details” (Information) för att se rapporten för verifiering av akustiska data. Det antal yttranden som godkändes och inte godkändes av verifieringen visas tillsammans med information om de yttranden som inte godkändes. I exemplet nedan misslyckades två WAV-filer med verifieringen på grund av felaktigt ljudformat (i den här datamängden hade en fil felaktig samplingsfrekvens och den andra fel filformat).

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-datasets-report.png)

Om du vill ändra datamängdens namn eller beskrivning kan du klicka på länken ”Edit” (Redigera) och ändra dessa poster. Obs! Du kan inte ändra ljudfilerna eller transkriptionerna.

## <a name="create-a-custom-acoustic-model"></a>Skapa en anpassad akustisk modell

När statusen för den akustiska datamängden är ”Complete” (Klar) kan den användas för att skapa en anpassad akustisk modell. Om du vill göra det klickar du på ”Acoustic Models” (Akustiska modeller) i listrutan ”Custom Speech” (Anpassat tal). En tabell med namnet ”Your models” (Dina modeller) visas med en lista över alla dina anpassade akustiska modeller. Den här tabellen är tom om det här är första användningen. Den aktuella nationella inställningen visas i tabellrubriken. För närvarande kan akustiska modeller endast skapas för amerikansk engelska.

För att skapa en ny modell klickar du på ”Create New” (Skapa ny) under tabellrubriken. På samma sätt som förut anger du ett namn och en beskrivning som hjälper dig att identifiera den här modellen. Till exempel kan fältet ”Description” (Beskrivning) användas för att registrera vilken startmodell och vilken akustisk datamängd som användes för att skapa modellen. Välj sedan en ”Base Acoustic Model” (Akustisk basmodell) i listrutan. Basmodellen är den modell som utgör startpunkten för anpassningen. Det finns två akustiska basmodeller att välja mellan. _Microsoft Search och Dictation AM_ (Microsofts akustiska modell för sökning och diktamen) är lämplig för tal som riktas mot ett program, till exempel kommandon, sökfrågor och diktamen. _Microsoft Conversational-modellen_ (Microsofts språkmodell för konversation) är lämplig för igenkänning av tal i konversationsstil. Den här typen av tal riktas vanligtvis mot en annan person och används i kundtjänst och i möten. Obs! Fördröjningen för partiella resultat i konversationsmodeller är högre än i sök- och diktamenmodeller.

Välj sedan de akustiska data som du vill använda för att utföra anpassningen med hjälp av listrutan.

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-create2.png)

Du kan välja att utföra offlinetestning av den nya modellen när bearbetningen är klar. Då körs en tal till text-utvärdering på en angiven akustisk datamängd med hjälp av den anpassade akustiska modellen, och resultatet rapporteras. Du utför det här testet genom att välja kryssrutan ”Accuracy Testing” (Noggrannhetstest). Välj sedan en språkmodell i listrutan. Om du inte har skapat några anpassade språkmodeller finns bara basspråkmodellerna i listrutan. Se [beskrivningen](cognitive-services-custom-speech-create-language-model.md) av basspråkmodellerna i guiden och välj den som passar bäst.

Välj slutligen den akustiska datamängd som du vill använda för att utvärdera den anpassade modellen. Om du utför noggrannhetstestning är det viktigt att välja akustiska data som skiljer sig från dem som användes för att skapa modellen för att få en realistisk uppfattning av modellens prestanda. Observera även att offlinetestning begränsas till 1 000 yttranden. Om den akustiska datamängden för testning är större än så utvärderas bara de första 1 000 yttrandena.

När du är redo att börja köra anpassningsprocessen trycker du på ”Create” (Skapa).

Då visas en ny post i tabellen för akustiska modeller som motsvarar den nya modellen. Status för processen återspeglas i tabellen. Statustillstånden är ”Waiting” (Väntar), ”Processing” (Bearbetar) och ”Complete” (Klar).

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utvecklat en anpassade akustisk modell för användning med ljudfiler och transkriptioner. Om du vill skapa en anpassad språkfil för användning med textfiler fortsätter du till självstudien om hur du skapar en anpassad språkmodell.

> [!div class="nextstepaction"]
> [Skapa en anpassad språkmodell](cognitive-services-custom-speech-create-language-model.md)
