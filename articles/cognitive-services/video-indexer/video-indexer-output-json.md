---
title: Granska utdata Azure Video Indexer | Microsoft Docs
description: Det här avsnittet undersöker resultatet Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355890"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Granska Video Indexer-utdata som genereras av v1 API

> [!Note]
> API: er för Video Indexer V1 nu är föråldrade och kommer att tas bort från den 1 augusti 2018. Du ska börja använda API: er för Video Indexer v2 för att undvika avbrott.
>
> Om du vill utveckla med API: er för Video Indexer-v2, se instruktionerna [här](https://api-portal.videoindexer.ai/). 

När du anropar den **hämta uppdelningar** API och svarsstatusen är OK, du får detaljerad JSON-utdata som svarsinnehållet. JSON-innehållet innehåller information om de angivna videoinsikter inklusive (avskrift OCRs, personer). Informationen innehåller nyckelord (ämnen), ansikten, block. Varje blockinkluderingar tidsintervall, avskrift rader, OCR rader, sentiment, ansikten, och blockera miniatyrerna.

Du kan använda den **hämta uppdelningar** API för att få fullständig uppdelning av en video som en JSON-innehåll.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Du kan också visuellt undersöka videons sammanfattade insikter genom att trycka på den **spela upp** knappen för videon i Video Indexer-portalen. Mer information finns i [visa och redigera videoinsikter](video-indexer-view-edit.md).

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Den här artikeln undersöker JSON-innehåll som returneras av den **hämta uppdelningar** API. Det kan vara bra att granska den [begrepp](video-indexer-concepts.md) artikeln.

> [!NOTE]
> Förfallodatum för åtkomsttoken i Video Indexer är en timme.

## <a name="root-elements"></a>Rotelement

Attribut | Beskrivning
---|---
id|Id för den här videon. Till exempel 63c6d532ff.
partition|En logisk partition som användaren kan ange i ladda upp för att söka efter den senare.
namn|Namnet på videon. Till exempel Azure Monitor.
beskrivning|Beskrivning av videon. Till exempel ”John Kemnetz med Scott Hanselman för att visa hur du låser upp kraften hos Azure övervakningsdata med Azure Monitor”.
Användarnamn|Skaparen av videon. Till exempel Channel 9-videor.
CreateTime |Tid som skapades. Exempel: 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Videon kan ha ett av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.
isOwned|TRUE om den aktuella användaren äger videon. Annars, FALSKT.  
isBase|SANT om en analys på detaljnivå är baserad på en video källa. FALSKT om en analys på detaljnivå är av en spellista som härleds från en annan analys på detaljnivå.
durationInSeconds|Varaktighet för videon.
summarizedInsights|Innehåller en [summarizedInsights](#summarizedInsights).
uppdelningar|Kan innehålla en eller flera [uppdelningar](#breakdowns)
Sociala|Innehåller en **sociala** element som beskriver antalet personer som gillar och vyer av videon.

## <a name="summarizedinsights"></a>summarizedInsights

Det här avsnittet visas en sammanfattning av insikterna.

Attribut | Beskrivning
---|---
namn|Namnet på videon. Till exempel Azure Monitor.
shortId|ID för videon. Till exempel 63c6d532ff.
privacyMode|Din analys på detaljnivå kan ha ett av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.
varaktighet|Innehåller en varaktighet som beskriver den tid som en insikt inträffade. Varaktighet är i sekunder.
thumbnailUrl|Videons miniatyr fullständiga URL: en. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”. Observera att om videon är privat, URL: en innehåller en åtkomsttoken för en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
ansikten|Kan innehålla en eller flera [ansikten](#faces)
ämnen|Kan innehålla en eller flera [ämnen](#topics)
sentiment|Kan innehålla en eller flera [sentiment](#sentiments)
audioEffects| Kan innehålla en eller flera [audioEffects](#audioEffects)
varumärken| Kan innehålla noll eller flera [varumärken](#brands)
Statistik|Mer information finns i [statistik](#Statistics)
.
### <a name="statistics"></a>Statistik

|Namn|Beskrivning|
|---|---|
|CorrespondenceCount|Antal svaren i videon.|
|WordCount|Antalet ord per talare.|
|SpeakerNumberOfFragments|Mängden fragment talaren har i en video.|
|SpeakerLongestMonolog|Talarens längsta monolog. Om talaren har silences inuti monolog ingår den. Åsidosatt inaktivitet i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på den tid som ägnats åt talarens monolog (utan intervallet mellan) dividerat med den totala tiden för videon. Tiden avrundas till tredje decimaltecknet.|

## <a name="breakdowns"></a>uppdelningar

Det här avsnittet visar information om insikterna.

Attribut | Beskrivning
---|---
id|Analys-ID. Till exempel 63c6d532ff.
state|Bearbetningstillstånd viss uppdelning-ID: t. Kan vara något av följande: överförda, bearbetning, bearbetade, misslyckade.
processingProgress|Förloppet. Till exempel 10%.
externalId| Du kan ange externalId under överföringen. Till exempel ”4f9c3500-eca7-4ab3-987e-a745017af698”. Du kan senare söka efter dina videor efter den här externa-ID.
externalUrl|Du kan ange externalUrl under överföringen. 
metadata|Du kan ange metadata under överföringen. 
insikter|Kan innehålla en eller flera [insikter](#insights)
thumbnailUrl|Videons miniatyr fullständiga URL: en. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”. Observera att om videon är privat, URL: en innehåller en åtkomsttoken för en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
publishedUrl|Publicerade URL: en. Till exempel ”https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest”.
viewToken|Ägartoken
sourceLanguage|Källspråk. Följande stöds: kinesiska, engelska, franska, tyska, italienska, japanska, portugisiska, ryska, spanska.
Språk|Språket i avskriften.

## <a name="insights"></a>insikter

Attribut | Beskrivning 
---|---
transcriptBlocks|Kan innehålla en eller flera [transcriptBlocks](#transcriptBlocks)
ämnen|Kan innehålla en eller flera [ämnen](#topics)
ansikten|Kan innehålla en eller flera [ansikten](#faces)
deltagare|Kan innehålla en eller flera [deltagare](#participants)
contentModeration|Kan innehålla en [contentModeration](#contentModeration)
audioEffectsCategories|Kan innehålla en eller flera [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>ansikten

### <a name="summarizedinsights"></a>summarizedInsights

**ansikten** som visas under **summarizedInsights**, visa en sammanfattning av varje ansikte i videon.

Attribut | Beskrivning 
---|---
id|ID för en person. Till exempel 11775.
shortId|Kort-ID. Eftersom en spellista kan härledas från flera uppdelningar, behövs detta ID för att ta reda på vilken av dessa uppdelningar är ursprungspunkten för varje ansikte.  
namn|Om de står inför är giltig, namnet på personen som har lagts till. Till exempel ”Scott Hanselman”. Om de står inför är okänt ”okänt #” har lagts till. 
beskrivning|Om de står inför är giltig fylls beskrivningen i baserat på API för Bing-sökning. I annat fall beskrivningen är **null**.
rubrik|Om de står inför är giltig fylls beskrivningen i baserat på API för Bing-sökning. Annars rubriken är **null**.
thumbnailFullUrl|De står inför miniatyr fullständiga URL: en. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”. Observera att om videon är privat, URL: en innehåller en åtkomsttoken för en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
utseende|Kan innehålla en eller flera [utseende](#appearances)
seenDuration|För hur länge ansikte har sett (i sekunder).
seenDurationRatio|Närvaro i förhållande till videons längd (0-1).

### <a name="breakdown-insights"></a>detaljnivå

**ansikten** som visas under **uppdelningar**, ge information om varje ansikte i videon.

Attribut | Beskrivning 
---|---
id|ID för en person. Till exempel 11775.
bingId|
namn|Om de står inför är giltig, namnet på personen som har lagts till. Till exempel ”Scott Hanselman”. Om de står inför är okänt ”okänt #” har lagts till. 
thumbnailId|Till exempel 616468f0 1636-4efa-94e7-262f2e575059.
beskrivning|Om de står inför är giltig fylls beskrivningen i baserat på API för Bing-sökning. I annat fall beskrivningen är **null**.
rubrik|Om de står inför är giltig fylls beskrivningen i baserat på API för Bing-sökning. Annars rubriken är **null**.
imageUrl|Den här URL: en pekar på en avbildning som hämtas från källan video.  
förtroende|Förtroendepoäng (är högre bättre).
knownPersonId|Id för en känd person (till exempel kändisar). Om en person inte är känd, innehåller ID: t nollor. Till exempel e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>ämnen

### <a name="summarizedinsights"></a>summarizedInsights

**avsnitt om** som visas under **summarizedInsights**, visa en sammanfattning av varje avsnitt som hittades i videon.

Attribut | Beskrivning 
---|---
namn|Ämnesnamn (till exempel ”Azure”). 
utseende|Kan innehålla en eller flera [utseende](#appearances).
isTranscript|SANT, om finns i en avskrift. Värdet är false om finns i en OCR.

### <a name="breakdown-insights"></a>detaljnivå

**avsnitt om** som visas under **uppdelningar**, ge information om varje avsnitt som hittades i videon.

|Attribut | Beskrivning |
|---|---|
|id|Unikt avsnittet-ID.|
|namn|Ämnesnamnet.|
|stem|Det här attributet används för närvarande inte.|
|ord|Det här attributet används för närvarande inte.|
|rangordning|Relevansgradering (är högre bättre).|

## <a name="sentiments"></a>sentiment

Attribut | Beskrivning
---|---
sentimentKey| Stöds för närvarande följande sentiment: positivt, Neutral, negativt. 
utseende|Kan innehålla en eller flera [utseende](#appearances)|.
seenDurationRatio|Närvaro i förhållande till videons längd (0-1).

## <a name="audioeffects"></a>audioEffects

Attribut | Beskrivning 
---|---
audioEffectKey| Giltiga värden är: tal, tystnad, HandClaps.
utseende|Kan innehålla en eller flera [utseende](#appearances)
seenDurationRatio|Närvaro i förhållande till videons längd (0-1).
seenDuration|För hur länge ljud effekten fanns (i sekunder).

## <a name="appearances"></a>utseende

Attribut | Beskrivning 
---|---
startTime| Tid-värde.
endTime|Tid-värde.
startSeconds| Tid-värde.
endSeconds| Tid-värde.

## <a name="participants"></a>deltagare

Attribut | Beskrivning 
---|---
id|ID för deltagaren.
namn|Namnet på deltagaren. Till exempel talare #1.
pictureUrl|Den **pictureUrl** attributet är reserverad för framtida användning.

## <a name="contentmoderation"></a>contentModeration

Attribut | Beskrivning 
---|---
adultClassifierValue|Konfidensnivån att videon har innehåll.
racyClassifierValue|Konfidensnivån att videon har olämpligt innehåll.
bannedWordsCount|Antalet ord som olämpligt språk. 
bannedWordsRatio|Förhållandet mellan svordomar ord från det totala antalet ord.
reviewRecommended|Booleskt värde som anger om videon bör granskas manuellt.
isAdult|Booleska värden som anger om videon betraktas som en vuxen video efter manuell granskning.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attribut | Beskrivning 
---|---
typ|ID för kategorin.
key|Något av följande: tal, tystnad, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Attribut | Beskrivning
---|---
id|ID för blocket.
rader|Kan innehålla en eller flera [rader](#lines)
sentimentIds|Den **sentimentIds** attributet är reserverad för framtida användning.
thumbnailIds|Den **thumbnailIds** attributet är reserverad för framtida användning.
sentiment|Attityden i blocket (0-1, negativt till positivt).
ansikten|Kan innehålla en eller flera [ansikten](#faces).
ocrs|Kan innehålla en eller flera [ocrs](#ocrs).
audioEffectInstances|Kan innehålla en eller flera [audioEffectInstances](#audioEffectInstances).
scener|Kan innehålla en eller flera [kulisserna](#scenes).
anteckningar|Kan innehålla noll eller flera [anteckningar](#annotations).

## <a name="ocrs"></a>ocrs

Beskriver vad välja i videon textinnehållet hittades. 

Attribut | Beskrivning 
---|---
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en timme video och använder bara 1 rad från den, till exempel 10:00-10:15. I så fall kan du har en spellista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.
rader|Kan innehålla en eller flera [rader](#lines).

## <a name="lines"></a>rader

### <a name="transcriptblocks"></a>transcriptBlocks

**rader** som visas under **transcriptBlocks**, beskriver rader med avskrifter finns i videon.

Attribut | Beskrivning 
---|---
id| ID för raden.
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en timme video och använder bara 1 rad från den, till exempel 10:00-10:15. I så fall kan du har en spellista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.
participantID| Id för API för den här raden.
text| Avskriften.
isIncluded| I grundläggande uppdelningar alltid true. I härledda spelningslistor raderna som ingick i videon, är inställda på isIncluded = true. Alla andra rader är falska.

### <a name="ocrs"></a>ocrs

**rader** som visas under **ocrs**, beskriver rader med OCRs hittades i videon.

Attribut | Beskrivning 
---|---
id|OCR-ID.
Bredd|Det här attributet används för närvarande inte.
Höjd|Det här attributet används för närvarande inte.
Språk|OCR-språk.
textData|OCR-text.
förtroende|Förtroendepoäng (är högre bättre).

## <a name="scenes"></a>scener

Attribut | Beskrivning 
---|---
id|Scen-ID.
timeRange|Innehåller en **timeRange**.
Bildrutan|Tid för viktiga ramen.
skärmbilder|Kan innehålla en eller flera [skärmbilder](#shots).

## <a name="shots"></a>skärmbilder

Attribut | Beskrivning 
---|---
id||Som ID.
timeRange|Innehåller en **timeRange**.
Bildrutan|Tid för viktiga ramen.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribut | Beskrivning 
---|---
typ|Index för händelsen ljud: munterhet = 1, HandClaps = 2, musik = 3, tal = 4, tystnad = 5
adressintervall|Kan innehålla en eller flera [intervall](#ranges).

## <a name="ranges"></a>adressintervall

**intervall** som visas under **audioEffectInstances**, beskriver ljud effekterna i dessa intervall.

Attribut | Beskrivning 
---|---
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till den aktuella spellistan. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en timme video och använder bara en rad från den, till exempel 10:00-10:15. I så fall kan du har en spellista med 1 rad där tidsintervallet är 10:00-10:15 men adjustedTimeRange är 00:00-00:15.

## <a name="annotations"></a>anteckningar

Returnerar taggar som baseras på identifierbara objekt, levande varelser, landskap, åtgärder och visual mönster.

|Attribut|Beskrivning|
|---|---|
|id|ID för anteckningen.|
|Namn|Namnet på anteckningen (till exempel Person, idrott spel, svart ramar).|
|Utseende|Kan innehålla en eller flera ansikten.|

## <a name="brands"></a>varumärken

Företag och produkten namn har identifierats i tal till textavskrift och/eller Video OCR. Detta inkluderar inte visuell igenkänning av varumärken eller logotyp identifiering.

Attribut | Beskrivning
---|---
id | ID för ett varumärke.
namn | Namnet på varumärke från Bing eller en anpassad varumärke.  
wikiId | Suffix för varumärke wikipedias url. Till exempel ”Target_Corporation” är suffixet för [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Varumärket är Wikipedias url, om det finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
förtroende | Förtroende-värdet för Video Indexer varumärke detektor (0-1).
beskrivning | Beskrivning av varumärket extraheras från Wikipedia. 
utseende | Kan innehålla en eller flera ansikten.
seenDuration | Närvaro i förhållande till videons längd (0-1).

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar dina egna analys på detaljnivå finns i [visa och redigera videoindexering](video-indexer-view-edit.md).

Information om hur du bäddar in widgetar i ditt program finns i [bädda in Video Indexer widgetar i dina program](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Se också

[API för Videoindexering](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Video Indexer-översikt](video-indexer-overview.md)

