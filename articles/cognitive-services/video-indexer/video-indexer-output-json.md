---
title: Granska utdata Azure Video indexeraren | Microsoft Docs
description: Det här avsnittet behandlar Video indexeraren utdata.
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
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Granska Video indexeraren utdata som genereras av API: n v1

> [!Note]
> API: er för Video indexeraren V1 är nu föråldrade och kommer att tas bort på 1 augusti 2018. Du bör börja använda API: er för Video indexeraren v2 för att undvika avbrott.
>
> För att utveckla med Video indexeraren v2 API: er finns i anvisningarna [här](https://api-portal.videoindexer.ai/). 

När du anropar den **hämta uppdelning** API och response-status är OK får du en detaljerad JSON-utdata som svar-innehåll. Innehållet i JSON innehåller information om de angivna video insikter inklusive (betyg OCRs personer). Informationen omfattar nyckelord (ämnen), ytor, block. Varje block innehåller tidsintervall, betyg rader, OCR rader, våra, ansikten, och blockera miniatyrerna.

Du kan använda den **hämta uppdelning** API för att få fullständig uppdelning av en video som en JSON-innehåll.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Du kan också visuellt undersöka videons sammanfattade insikter genom att trycka på **spela upp** video i Video indexeraren portal-knappen. Mer information finns i [visa och redigera video insikter](video-indexer-view-edit.md).

![Insikter](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Den här artikeln går igenom innehållet i JSON som returneras av den **hämta uppdelning** API. Det kan vara bra att gå igenom den [begrepp](video-indexer-concepts.md) artikel.

> [!NOTE]
> Förfallodatum för alla åtkomst-token i videon indexerare är en timme.

## <a name="root-elements"></a>Rotelement

Attribut | Beskrivning
---|---
id|Id för den här videon. Till exempel 63c6d532ff.
partition|En logisk partition som användaren kan ange i överför för att söka efter den senare.
namn|Namnet på videon. Till exempel Azure-Monitor.
description|Beskrivning av videon. Till exempel ”John Kemnetz kopplar Scott Hanselman för att visa hur du låser upp kraften hos Azure övervakningsdata med Azure-Monitor”.
Användarnamn|Skapare av videon. Till exempel Channel9 videor.
CreateTime |Tid som skapades. Till exempel 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Videon kan ha en av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.
isOwned|TRUE om den aktuella användaren äger videon. Annars, FALSKT.  
isBase|SANT om en analys på detaljnivå baseras på en video källa. FALSKT om fördelningen är av en spellista som härleds från en annan uppdelning.
durationInSeconds|Varaktighet för videon.
summarizedInsights|Innehåller en [summarizedInsights](#summarizedInsights).
uppdelning|Kan innehålla en eller flera [uppdelning](#breakdowns)
Sociala|Innehåller en **sociala** element som beskriver antalet önskemål och vyer av videon.

## <a name="summarizedinsights"></a>summarizedInsights

Det här avsnittet visas en sammanfattning av insikter.

Attribut | Beskrivning
---|---
namn|Namnet på videon. Till exempel Azure-Monitor.
shortId|ID för videon. Till exempel 63c6d532ff.
privacyMode|Din analys på detaljnivå kan ha en av följande lägen: **privata**, **offentliga**. **Offentliga** -videon är synlig för alla i ditt konto och vem som helst som har en länk till videon. **Privata** -videon är synlig för alla i ditt konto.
varaktighet|Innehåller en varaktighet som beskriver den tid som en förklaring inträffade. Det är varaktighet i sekunder.
thumbnailUrl|Miniatyr av video fullständig URL. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”. Observera att om videon är privat kan URL: en innehåller en åtkomst-token med en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
ytor|Kan innehålla en eller flera [ytor](#faces)
Avsnitt om|Kan innehålla en eller flera [avsnitt](#topics)
våra|Kan innehålla en eller flera [våra](#sentiments)
audioEffects| Kan innehålla en eller flera [audioEffects](#audioEffects)
varumärken| Kan innehålla noll eller fler [varumärken](#brands)
Statistik|Mer information finns i [statistik](#Statistics)
.
### <a name="statistics"></a>Statistik

|Namn|Beskrivning|
|---|---|
|CorrespondenceCount|Antal svaren i videon.|
|WordCount|Antal ord per högtalaren.|
|SpeakerNumberOfFragments|Mängden fragment högtalaren har i en video.|
|SpeakerLongestMonolog|På högtalaren längsta monolog. Om högtalaren har silences inuti monolog ingår den. Tystnad i början och slutet av monolog tas bort.| 
|SpeakerTalkToListenRatio|Beräkningen baseras på tidsåtgång på högtalaren monolog (utan tystnad mellan) dividerat med den totala tiden av videon. Tiden avrundas till tredje decimaltecknet.|

## <a name="breakdowns"></a>uppdelning

Det här avsnittet visar information om insikter.

Attribut | Beskrivning
---|---
id|Uppdelning-ID. Till exempel 63c6d532ff.
state|Det angivna uppdelning-id bearbetningstillstånd. Kan vara något av följande: överförda, bearbetning, bearbetade, misslyckades.
processingProgress|Status. Till exempel 10%.
externalId| Du kan ange externalId under överföringen. Till exempel ”4f9c3500-eca7-4ab3-987e-a745017af698”. Senare kan du söka efter dina videor efter den här externa-ID.
externalUrl|Du kan ange externalUrl under överföringen. 
metadata|Du kan ange metadata under överföringen. 
insikter|Kan innehålla en eller flera [insikter](#insights)
thumbnailUrl|Miniatyr av video fullständig URL. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO.”. Observera att om videon är privat kan URL: en innehåller en åtkomst-token med en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
publishedUrl|Publicerade URL. Till exempel ”https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest”.
viewToken|Ägartoken
sourceLanguage|Språket som källa. Följande stöds: kinesiska, engelska, franska, tyska, italienska, japanska, portugisiska, ryska, spanska.
språk|Språk för betyg.

## <a name="insights"></a>insikter

Attribut | Beskrivning 
---|---
transcriptBlocks|Kan innehålla en eller flera [transcriptBlocks](#transcriptBlocks)
Avsnitt om|Kan innehålla en eller flera [avsnitt](#topics)
ytor|Kan innehålla en eller flera [ytor](#faces)
deltagare|Kan innehålla en eller flera [deltagare](#participants)
contentModeration|Kan innehålla en [contentModeration](#contentModeration)
audioEffectsCategories|Kan innehålla en eller flera [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>ytor

### <a name="summarizedinsights"></a>summarizedInsights

**ytor** som visas under **summarizedInsights**, visa en sammanfattning av varje står inför hittades i videon.

Attribut | Beskrivning 
---|---
id|ID för en person. Till exempel 11775.
shortId|Kort-ID. Eftersom en spellista kan härledas från flera uppdelning, behövs detta ID för att ta reda på vilken av dessa fördelningar är ursprungspunkten för varje yta.  
namn|Om de står inför är giltig, namnet på personen som har lagts till. Till exempel ”Scott Hanselman”. Om de står inför är okänt ”okänt #” har lagts till. 
description|Om de står inför är giltig fylls beskrivningen i baserat på Bing API-sökningen. Annars beskrivningen är **null**.
rubrik|Om de står inför är giltig fylls beskrivningen i baserat på Bing API-sökningen. Annars rubriken är **null**.
thumbnailFullUrl|De står inför miniatyr fullständig URL. Till exempel ”https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...”. Observera att om videon är privat kan URL: en innehåller en åtkomst-token med en timme. Efter en timme URL: en kommer inte längre och du behöver hämta uppdelning igen med en ny url i den eller anropa GetAccessToken för att få en ny åtkomsttoken och skapa hela webbadressen manuellt (”https://www.videoindexer.ai/api/Thumbnail/[shortId] / [ThumbnailId]? accessToken = [accessToken]').
utseende|Kan innehålla en eller flera [utseende](#appearances)
seenDuration|För hur länge (i sekunder) påträffades.
seenDurationRatio|Förekomst i förhållande till video varaktighet (0-1).

### <a name="breakdown-insights"></a>uppdelning insikter

**ytor** som visas under **uppdelningen**, beskriver information om varje yta som hittades i videon.

Attribut | Beskrivning 
---|---
id|ID för en person. Till exempel 11775.
bingId|
namn|Om de står inför är giltig, namnet på personen som har lagts till. Till exempel ”Scott Hanselman”. Om de står inför är okänt ”okänt #” har lagts till. 
thumbnailId|Till exempel 616468f0 1636-4efa-94e7-262f2e575059.
description|Om de står inför är giltig fylls beskrivningen i baserat på Bing API-sökningen. Annars beskrivningen är **null**.
rubrik|Om de står inför är giltig fylls beskrivningen i baserat på Bing API-sökningen. Annars rubriken är **null**.
imageUrl|URL: en pekar på en avbildning som hämtas från källan video.  
förtroende|Förtroende-poäng (är högre bättre).
knownPersonId|Id för en känd person (till exempel ryktbarhet). Om en person är okänt innehåller id nollor. Till exempel e3eaff5f ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>Avsnitt om

### <a name="summarizedinsights"></a>summarizedInsights

**avsnitt om** som visas under **summarizedInsights**, visa en sammanfattning av varje avsnitt som hittades i videon.

Attribut | Beskrivning 
---|---
namn|Avsnittsnamnet (till exempel ”Azure”). 
utseende|Kan innehålla en eller flera [utseende](#appearances).
isTranscript|True, om finns i ett betyg. FALSKT om hittades i ett OCR.

### <a name="breakdown-insights"></a>uppdelning insikter

**avsnitt om** som visas under **uppdelningen**, beskriver information om varje avsnitt i videon.

|Attribut | Beskrivning |
|---|---|
|id|Unik avsnittet-ID.|
|namn|Namnet på ämnet.|
|stam|Det här attributet används för närvarande inte.|
|ord|Det här attributet används för närvarande inte.|
|Rangordning|Relevans poäng (är högre bättre).|

## <a name="sentiments"></a>våra

Attribut | Beskrivning
---|---
sentimentKey| För närvarande följande våra stöds: positivt, Neutral negativt. 
utseende|Kan innehålla en eller flera [utseende](#appearances)|.
seenDurationRatio|Förekomst i förhållande till video varaktighet (0-1).

## <a name="audioeffects"></a>audioEffects

Attribut | Beskrivning 
---|---
audioEffectKey| Giltiga värden är: tal, tystnad, HandClaps.
utseende|Kan innehålla en eller flera [utseende](#appearances)
seenDurationRatio|Förekomst i förhållande till video varaktighet (0-1).
seenDuration|För hur länge ljud effekten fanns (i sekunder).

## <a name="appearances"></a>utseende

Attribut | Beskrivning 
---|---
startTime| Tidsvärdet.
endTime|Tidsvärdet.
startSeconds| Tidsvärdet.
endSeconds| Tidsvärdet.

## <a name="participants"></a>deltagare

Attribut | Beskrivning 
---|---
id|ID för deltagaren.
namn|Namnet på deltagaren. Till exempel talare #1.
pictureUrl|Den **pictureUrl** attributet är reserverad för framtida användning.

## <a name="contentmoderation"></a>contentModeration

Attribut | Beskrivning 
---|---
adultClassifierValue|Konfidensnivån att videon har vuxet innehåll.
racyClassifierValue|Konfidensnivån att videon har dyr innehåll.
bannedWordsCount|Antal svordomar ord. 
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
Sentiment|Sentiment i blocket (0-1, negativt till positivt).
ytor|Kan innehålla en eller flera [ytor](#faces).
ocrs|Kan innehålla en eller flera [ocrs](#ocrs).
audioEffectInstances|Kan innehålla en eller flera [audioEffectInstances](#audioEffectInstances).
i bakgrunden|Kan innehålla en eller flera [i bakgrunden](#scenes).
Anteckningar|Kan innehålla noll eller fler [anteckningar](#annotations).

## <a name="ocrs"></a>ocrs

Beskriver vad välja i videon textinnehållet hittades. 

Attribut | Beskrivning 
---|---
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till aktuell spelningslista. Eftersom du kan skapa en spellista från olika rader med olika videor måste du ta en timme video och använder bara 1 rad från den, till exempel 10:00 – 10:15. I så fall måste en spellista med 1 rad där tidsintervallet 10:00 – 10:15 men adjustedTimeRange är 00:00-00:15.
rader|Kan innehålla en eller flera [rader](#lines).

## <a name="lines"></a>rader

### <a name="transcriptblocks"></a>transcriptBlocks

**rader** som visas under **transcriptBlocks**, beskriver raderna i betyg i videon.

Attribut | Beskrivning 
---|---
id| ID för raden.
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till aktuell spelningslista. Eftersom du kan skapa en spellista från olika rader med olika videor måste du ta en timme video och använder bara 1 rad från den, till exempel 10:00 – 10:15. I så fall måste en spellista med 1 rad där tidsintervallet 10:00 – 10:15 men adjustedTimeRange är 00:00-00:15.
participantID| Id för talare för den här raden.
text| Betyg.
isIncluded| I grundläggande uppdelning är alltid true. I härledda spellistor raderna som ingick i videon, är inställda på att isIncluded = true. Alla andra rader är false.

### <a name="ocrs"></a>ocrs

**rader** som visas under **ocrs**, beskriver raderna i OCRs hittades i videon.

Attribut | Beskrivning 
---|---
id|OCR-ID.
Bredd|Det här attributet används för närvarande inte.
Höjd|Det här attributet används för närvarande inte.
språk|OCR-språk.
textData|Texten som OCR.
förtroende|Förtroende-poäng (är högre bättre).

## <a name="scenes"></a>i bakgrunden

Attribut | Beskrivning 
---|---
id|Den scen-ID.
timeRange|Innehåller en **timeRange**.
keyFrame|Tid för viktiga ramen.
bilderna|Kan innehålla en eller flera [bilderna](#shots).

## <a name="shots"></a>bilderna

Attribut | Beskrivning 
---|---
id||Som ID.
timeRange|Innehåller en **timeRange**.
keyFrame|Tid för viktiga ramen.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribut | Beskrivning 
---|---
typ|Index för händelsen ljud: munterhet = 1, HandClaps = 2, musik = 3, tal = 4, tystnad = 5
adressintervall|Kan innehålla en eller flera [intervall](#ranges).

## <a name="ranges"></a>adressintervall

**adressintervall** som visas under **audioEffectInstances**, beskriver ljud effekter i dessa intervall.

Attribut | Beskrivning 
---|---
timeRange|Tidsintervall i den ursprungliga videon.
adjustedTimeRange|AdjustedTimeRange är tidsintervallet i förhållande till aktuell spelningslista. Eftersom du kan skapa en spellista från olika rader med olika videor, kan du ta en timme video och Använd bara en rad från den, till exempel 10:00 – 10:15. I så fall måste en spellista med 1 rad där tidsintervallet 10:00 – 10:15 men adjustedTimeRange är 00:00-00:15.

## <a name="annotations"></a>Anteckningar

Returnerar taggar baserat på okänt objekt, levande människor, landskap, åtgärder och visual mönster.

|Attribut|Beskrivning|
|---|---|
|id|ID för anteckningen.|
|Namn|Namnet på anteckningen (till exempel en Person, inom spel, svart ramar).|
|Utseende|Kan innehålla en eller flera utseende.|

## <a name="brands"></a>varumärken

Företag och produktens namn upptäcktes i tal till text betyg och/eller Video OCR. Detta inkluderar inte visual igenkänning av varumärken och logotyper identifiering.

Attribut | Beskrivning
---|---
id | ID för ett varumärke.
namn | Namnet på varumärken från Bing eller en anpassad varumärken.  
wikiId | Suffix för varumärken wikipedia url. Till exempel ”Target_Corporation” är suffixet för [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Varumärke har Wikipedia url, om det finns. Till exempel [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
förtroende | Förtroende-värdet för Video indexeraren varumärken detektorn (0-1).
description | Beskrivning av varumärke extraheras från Wikipedia. 
utseende | Kan innehålla en eller flera utseende.
seenDuration | Förekomst i förhållande till video varaktighet (0-1).

## <a name="next-steps"></a>Nästa steg

Information om hur du skapar egna uppdelning finns [visa och redigera Video indexeraren insikter](video-indexer-view-edit.md).

Information om hur du bäddar in widgetar i ditt program finns [bädda in Video indexeraren widgetar i dina program](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Se också

[Video indexeraren API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Indexeraren videoöversikt](video-indexer-overview.md)

