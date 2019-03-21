---
title: 'Självstudier: Skapa en akustisk modell med Speech Service'
titlesuffix: Azure Cognitive Services
description: Lär dig hur du skapar en akustisk modell med Speech Services på Azure.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: f2a111558fa3f515b797745dc51e32f625bbd91f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57844032"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Självstudier: Skapa en anpassad akustisk modell

Att skapa en anpassad akustisk modell är användbart om ditt program är avsett för användning i en viss miljö, till exempel en bil, med specifika inspelningsenheter eller villkor, eller av en viss användargrupp. Exempel innefattar tal med brytning, vissa bakgrundsljud eller användning av en särskild mikrofon för inspelning.

I den här artikeln kan du se hur du:
> [!div class="checklist"]
> * Förbereda data
> * Importera den akustiska datamängden
> * Skapa den anpassade akustiska modellen

Om du inte har ett Azure Cognitive Services-konto skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Se till att ditt Cognitive Services-konto är anslutet till en prenumeration genom att öppna sidan [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Cognitive Services-prenumerationer).

Du kan ansluta till en Speech Services-prenumeration som har skapats i Azure portal genom att välja **ansluta befintliga prenumeration**.

Information om hur du skapar en Speech Services-prenumeration i Azure-portalen finns i [testa Speech Services kostnadsfritt](get-started.md).

## <a name="prepare-the-data"></a>Förbereda data

För att anpassa en akustisk modell för en viss domän krävs en samling taldata. Den här samlingen kan variera mellan några yttranden till flera hundra timmars tal. Samlingen består av en uppsättning ljudfiler med taldata och en textfil med transkriptioner av varje ljudfil. Ljuddata ska vara representativa för det scenario där du vill använda igenkännaren.

Exempel:

* Om du vill känna igen tal bättre i en fabriksmiljö med mycket oljud bör ljudfilen bestå av personer som talar i en högljudd fabrik.
* Om du är intresserad av att optimera prestanda för en enda talare – till exempel om vill transkribera alla Franklin Roosevelts Fireside Chats-tal – bör ljudfilerna bestå av många exempel på endast den talaren.

En akustisk datauppsättning för att anpassa den akustiska modellen består av två delar: (1) en uppsättning ljudfiler som innehåller taldata och (2) en fil som innehåller transkriptioner av alla ljudfiler.

### <a name="audio-data-recommendations"></a>Rekommendationer för ljuddata

* Alla ljudfiler i datamängden bör lagras i ljudformatet WAV (RIFF).
* Ljudet måste ha en samplingsfrekvens på 8 kilohertz (kHz) eller 16 kHz, och exempelvärdena ska lagras som okomprimerade, pulskodsmodulerade (PCM) 16-bitars heltal med tecken (short).
* Endast ljudfiler med en kanal (mono) stöds.
* Ljudfilen kan vara mellan 100 mikrosekunder och en minut lång, men bör vara kring 10-12 sekunder. Varje ljudfil ska helst börja och sluta med minst 100 mikrosekunders tystnad; mellan 500 mikrosekunder och 1 sekund är vanligt.
* Om du har bakgrundsljud i dina data rekommenderar vi att du även har några exempel med längre tystnadsperioder i dina data – till exempel ett par sekunder – före och/eller efter det talade innehållet.
* Varje ljudfil bör bestå av ett enda uttryck – till exempel en enskild mening för diktering, en enskild fråga eller omgång av en dialog.
* Varje ljudfil i datamängden ska ha ett unikt filnamn och filnamnstillägget .wav.
* Uppsättningen ljudfiler ska placeras i en enskild mapp utan underkataloger, och hela uppsättningen ljudfiler ska packas ned som ett enda .zip-filarkiv.

> [!NOTE]
> Dataimporter via webbportalen är för närvarande begränsade till 2 GB, vilket är den maximala storleken för en akustisk datamängd. Denna storlek motsvarar cirka 17 timmars ljud som spelats in med 16 kHz eller 34 timmars ljud som spelats in med 8 kHz. De grundläggande kraven för ljuddata sammanfattas i följande tabell:
>

| Egenskap  | Värde |
|---------- |----------|
| Filformat | RIFF (WAV) |
| Samplingsfrekvens | 8 000 Hertz (Hz) eller 16 000 Hz |
| Kanaler | 1 (mono) |
| Samplingsformat | PCM, 16-bitars heltal |
| Filens varaktighet | 0,1 sekunder < varaktighet < 12 sekunder |
| Tystnadsmarginal | > 0,1 sekunder |
| Arkivformat | .zip |
| Maximal arkivstorlek | 2 GB |

> [!NOTE]
> Filnamn ska endast använda latinska tecken och följa formatet ”filnamn.filnamnstillägg”

## <a name="language-support"></a>Stöd för språk

En fullständig lista över språk som stöds för anpassade **Tal till text**-språkmodeller finns i avsnittet om [språk som stöds för Speech-tjänsten](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Transkriptioner för ljuddatamängden

Transkriptioner för alla WAV-filer bör ingå i en enda fil med oformaterad text. Varje rad i transkriptionsfilen ska innehålla namnet på en av ljudfilerna följt av motsvarande transkription. Filnamnet och transkriptionen ska separeras med ett tabbtecken (\t).

  Exempel:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Transkriptionen ska kodas som UTF-8-byteordningsmärke (BOM).

Transkriptionerna textnormaliseras så att de kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data laddas upp till Custom Speech Service. Information om det språk som är lämpligt att använda när du förbereder transkriptionerna finns i [riktlinjerna om transkription för användning av Speech-tjänsten](prepare-transcription.md).

Utför stegen i nästa avsnitt med hjälp av den [Speech Services portal](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Importera den akustiska datamängden

När du har förberett ljudfilerna och transkriptionerna är de redo att importeras till tjänstwebbportalen.

Om du vill importera dem först se till att du är inloggad på den [Speech Services portal](https://cris.ai). I listrutan **Custom Speech** i menyfliksområdet väljer du **Adaptation Data** (Anpassningsdata). Om det här är första gången du laddar upp data till Custom Speech Service visas en tom tabell med namnet **Datasets** (Datamängder).

På raden **Acoustic Datasets** (Akustiska datamängder) väljer du knappen **Import** (Importera), så visar platsen en sida för att ladda upp en ny datamängd.

![Sidan Importera akustiska data](media/stt/speech-acoustic-datasets-import.png)

I rutorna **Namn** och **Beskrivning** anger du nödvändig informationen. Minnesvärda beskrivningar är användbara för att hålla reda på de olika datamängder som du laddar upp.

I rutorna **Transcriptions file (.txt)** och **Audio files (.zip)** väljer du **Bläddra** och väljer sedan filen med transkription i klartext respektive zip-filen med WAV-filer. När förberedelsen är klar väljer du **Import** (Importera) för att ladda upp data. Dina data laddas upp. För större datamängder kan importprocessen ta flera minuter.

När uppladdningen är klar går du tillbaka tabellen **Acoustic Datasets** (Akustiska datamängder). En post visas som motsvarar din akustiska datamängd. Observera att den har tilldelats ett unikt ID (GUID). Data visar aktuell status: *NotStarted* (Inte påbörjat) när de har placerats i kö för bearbetning, *Running* (Körs) medan de verifieras och *Complete* (Slutfört) när de är redo att användas.

Dataverifieringen innehåller en rad kontroller av ljudfilerna för att verifiera filformat, längd och samplingsfrekvens samt kontroller av transkriptionsfilerna för att verifiera filformatet och utföra viss textnormalisering.

När statusen är *Succeeded* (Lyckades) kan du välja **Details** (Information) för att visa rapporten för verifiering av akustiska data. Det antal yttranden som godkändes och inte godkändes av verifieringen visas tillsammans med information om de yttranden som inte godkändes. I exemplet på följande bild misslyckades verifieringen för två WAV-filer på grund av felaktigt ljudformat. I den här datamängden har den ena filen felaktig samplingsfrekvens, och den andra har felaktigt filformat.

![Sidan Anpassning av datainformation](media/stt/speech-acoustic-datasets-report.png)

Om du vill ändra namn eller beskrivning för datamängden kan du välja länken **Edit** (Redigera) och ändra deras poster. Du kan inte ändra posterna för transkriptions- eller ljudfiler.

## <a name="create-a-custom-acoustic-model"></a>Skapa en anpassad akustisk modell

När statusen för den akustiska datamängden är *Complete* (Klar) kan du använda datamängden för att skapa en anpassad akustisk modell. Om du vill göra det väljer du **Acoustic Models** (Akustiska modeller) i listrutan **Custom Speech** (Anpassat tal). En tabell som är märkt **Your models** (Dina modeller) visar en lista över alla dina anpassade akustiska modeller. Den här tabellen är tom om det här är den första användningen. Tabellrubriken visar den aktuella nationella inställningen. För närvarande kan du endast skapa akustiska modeller för amerikansk engelska.

För att skapa en ny modell väljer du **Create New** (Skapa ny) under tabellrubriken. På samma sätt som förut anger du ett namn och en beskrivning som hjälper dig att identifiera den här modellen. Till exempel kan du använda fältet **Description** (Beskrivning) för att registrera vilken startmodell och vilken akustisk datamängd som du använde för att skapa modellen.

I listrutan **Base Acoustic Model** (Akustisk basmodell) väljer du sedan en basmodell. Basmodellen är startpunkten för din anpassning. Det finns två akustiska basmodeller att välja mellan:
* Modellen **Microsoft Search and Dictation AM** (Microsofts akustiska basmodell för sökning och diktamen) är lämplig för tal som riktas mot ett program, till exempel kommandon, sökfrågor och diktamen.
* **Microsoft Conversational Model** (Microsofts konversationsmodell) är lämplig för igenkänning av tal i konversationsstil. Den här typen av tal riktas vanligtvis mot en annan person och används i kundtjänst och i möten.

Fördröjningen för partiella resultat i konversationsmodeller är högre än i sök- och diktamenmodeller.

> [!NOTE]
> För närvarande lanserar vi vår nya **Universal-** modell, som syftar till att hantera alla scenarier. De tidigare nämnda modellerna kommer att förbli offentligt tillgängliga.

I listrutan **Acoustic Data** (Akustiska data) väljer du sedan de akustiska data som du vill använda för att utföra anpassningen.

![Sidan Skapa akustisk modell](media/stt/speech-acoustic-models-create2.png)

När bearbetningen är klar kan du välja att utföra noggrannhetstestning av den nya modellen. Det här testet kör en tal till text-utvärdering på en angiven akustisk datamängd med hjälp av den anpassade akustiska modellen och rapporterar sedan resultatet. Du utför det här testet genom att välja kryssrutan **Accuracy Testing** (Noggrannhetstest). I listrutan väljer du sedan en språkmodell. Om du inte har skapat några anpassade språkmodeller visas bara basspråkmodellerna i listrutan. Information om hur du väljer den lämpligaste språkmodellen finns i [Självstudie: Skapa en anpassad språkmodell](how-to-customize-language-model.md).

Välj slutligen den akustiska datamängd som du vill använda för att utvärdera den anpassade modellen. Om du utför noggrannhetstestning för att få en realistisk uppfattning av modellens prestanda är det viktigt att välja en akustiska datamängd som skiljer sig från den du använde för att skapa modellen. Noggrannhetstestning av träningsdata kan inte användas för att utvärdera hur den anpassade modellen presterar under verkliga förhållanden. Resultatet blir för optimistiskt. Observera också att noggrannhetstestning är begränsad till 1 000 yttranden. Om den akustiska datamängden för testning är större utvärderas bara de första 1 000 yttrandena.

När du är redo att börja köra anpassningsprocessen väljer du **Create** (Skapa).

Tabellen med akustiska modeller visar en ny post som motsvarar den här nya modellen. Tabellen visar även status för processen: *Waiting* (Väntar), *Processing* (Bearbetar) eller *Complete* (Slutfört).

![Sidan Akustiska modeller](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech Services](https://azure.microsoft.com/try/cognitive-services/)
- [Identifiera tal i C#](quickstart-csharp-dotnet-windows.md)
- [Git-exempeldata](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
