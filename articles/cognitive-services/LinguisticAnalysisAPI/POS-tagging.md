---
title: En del av tal-märkning i språkliga analys API | Microsoft Docs
description: Lär dig hur en del av tal taggning i Microsoft kognitiva Services identifierar kategori- eller en del av tal varje ord med text.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 35d2042db5f8d64c7dbd1df6bfdebcba6d6aab28
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351798"
---
# <a name="part-of-speech-tagging"></a>En del av tal taggning

## <a name="background-and-motivation"></a>Bakgrund och syfte

När en text har separerats i meningar och token, är analys nästa steg att identifiera kategori- eller en del av tal varje ord.
Dessa inkluderar kategorier som *substantiv* (som vanligtvis motsvarar personer, platser, saker, idéer, etc.) och *verb* (som vanligtvis motsvarar åtgärder, ändrar tillstånd, etc. För vissa ord är en del av tal entydigt (till exempel *quagmire* verkligen är endast ett substantiv), men för många andra är det svårt att se.
*Tabellen* kan vara en plats där du sitta (eller 2D-layout av siffror), men du kan också ”tabell en diskussion”.

## <a name="list-of-part-of-speech-tags"></a>Lista över en del av tal taggar

| Tagga | Beskrivning | Exempel ord |
|-----|-------------|---------------|
| $ | dollar | $ |
| \`\` | inledande citattecken | \` \`\` |
| '' | avslutande citattecken | ' '' |
| ( | vänsterparentes | ( [ { |
| ) | avslutande parentes | ) ] } |
| , | kommatecken | , |
| -- | streck | -- |
| . | meningen Begränsare | . ! ? |
| : | kolon eller punkter | : ; ... |
| Kopia | tillsammans, samordna | och men eller ännu|
| CD | siffror, kardinalkurvelementets | nio 20 1980 ' 96 |
| DT | determiner |en av en alla båda varken|
| EX | existentiell där | Det |
| FW | externa word | enfant terrible hoi polloi je ne sais quoi |
| I | preposition eller underordna tillsammans| i innanför om vid om |
| JJ | adjektiv eller siffror, ordningstal | nionde ganska execrable multimodal |
| JJR | adjektiv, jämförande | bättre snabbare billigare |
| JJS | adjektiv, superlative | bästa snabbaste billigaste | 
| LS | lista över objekt markör | (a) (b) 1 2 A B A. B. |
| MD | modal tillägg | kan kan skall kommer kunde kan bör borde |
| NN | substantiv vanliga, enkel eller vikt | tilldelats pengar sko |
| NNP | substantiv, korrekt, enda | Kennedy Roosevelt Chicago Weehauken |
| NNPS | substantiv, korrekt, plural | Springfields buskar |
| NNS | substantiv, vanliga, plural | delar möss fält |
| (stillahavstid) | före determiner | alla både halv många ganska sådana till detta |
| POS | genitive markör | ' 's |
| PRP | pronomen, personliga | hon han den jag vi de du |
| PRP$ | pronomen, Possessiv | hennes hans dess min våra sina din |
| RB | adverb | kliniskt endast |
| RBR | ett adverb jämförande | ytterligare gloomier grander graver större grimmer svårare harsher sundare tyngre högre men större senare smidigare längre mindre perfekt mindre lonelier längre starkare nedre mer... |
| RBS | adverb, superlative | bästa största bluntest tidigaste längst första längst svåraste heartiest högsta största minst mindre de flesta närmaste andra hårdaste sämsta |
| RP | Partikeln | på inaktiverat upp ut om |
| SYM | symbol | % & |
| Till | ”till” som preposition eller infinitive markör | till |
| VISST | interjection | visst vilken fröjdefull jul howdy hello |
| VB | basformulär-verb | ge tilldela direkt |
| VBD | tidigare Tempus-verb | gav tilldelade flög |
| VBG | verb, finns participle eller gerund | ger tilldela flyga |
| VBN | tidigare participle-verb | angivna tilldelade leds |
| VBP | verb, presens, inte 3 person som enda | ge tilldela direkt |
| VBZ | verb, presens, 3 person som enda | ger tilldelas kommer |
| WDT | Vad determiner | som vilka som |
| WP | Vad pronomen | vem som |
| WP$ | Vad pronomen Possessiv | vars |
| WRB | Vad adverb | hur men när där |

## <a name="specification"></a>Specifikationen

För tokenisering, vi förlitar sig på specifikationen från den [Sandberg Treebank](https://www.cis.upenn.edu/~treebank/).
