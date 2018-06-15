---
title: Vad är tal service (förhandsgranskning)? | Microsoft Docs
description: 'Tjänsten tal, en del av Microsofts kognitiva tjänster, unites flera Azure tal-tjänster som tidigare var tillgängliga separat: tal för Bing (bestående av taligenkänning och text till tal), anpassade tal och tal Translation.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: e7c09eee1634c52e78a523a7cc65641ea99f23e6
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356169"
---
# <a name="what-is-the-speech-service-preview"></a>Vad är tal service (förhandsgranskning)?

Tjänsten tal, en del av Microsofts kognitiva tjänster, unites flera Azure tal-tjänster som tidigare var tillgängliga separat: tal för Bing (bestående av taligenkänning och text till tal), anpassade tal och tal Translation. Som dess ämnen drivs tjänsten tal av den teknik som används i andra Microsoftprodukter, inklusive Cortana och Microsoft Office.

> [!NOTE]
> Dikterings-tjänsten är för närvarande i förhandsversion. Återvända hit regelbundet efter uppdateringar till dokumentationen och ytterligare kodexempel.

Med en prenumeration kan tjänsten enhetlig tal utvecklare ett enkelt sätt att ge sina program kraftfulla tal-aktiverade funktioner. Dina appar kan nu funktionen röst kommandot, skrivfel, diktamen, tal sammanfattande och översättning.

|Funktion|Beskrivning|
|-|-|
|Tal till text|Konverterar kontinuerlig tal till text som kan användas som indata till ditt program. Kan integreras med den [språk förstå service](https://docs.microsoft.com/azure/cognitive-services/luis/) (THOMAS) för att härleda användaravsikt från utterances.|
|Text till tal|Texten omvandlas till ljudfiler av naturliga låta syntetiskt tal.|
|Tal&nbsp;översättning|Ange översättningar av tal till andra språk med text eller tal utdata.|

## <a name="using-the-speech-service"></a>Med hjälp av tjänsten tal

Tjänsten tal görs tillgänglig på två sätt. [SDK](speech-sdk.md) avlägsnar in information om nätverksprotokoll. Den [REST API](rest-apis.md) fungerar med alla programmeringsspråk, men erbjuder inte alla funktioner som erbjuds av SDK.

|<br>Metod|Tal<br>till Text|Text som ska<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[SDK: er](speech-sdk.md)|Ja|Nej|Ja|Bibliotek för specifika programmeringsspråk som förenklar utveckling.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i ditt program.|

## <a name="speech-to-text"></a>Tal till text

Den [tal till Text](speech-to-text.md) (STT) eller taligenkänning API transcribes ljudströmmar till text som programmet kan acceptera som indata. Programmet kan sedan, till exempel ange texten i ett dokument eller agera på den som ett kommando.

Tal till Text separat har optimerats för interaktiv konversationen och dictation scenarier. Nedan följer vanliga användningsområden för tal Text-API: et. 

* Identifiera kort en utterance, till exempel ett kommando, utan mellanliggande resultat
* Transkribera långa, inspelade utterance, till exempel ett röstmeddelande
* Transkribera strömmande tal i realtid, med ofullständiga resultat för dictation
* Bestämma vad användare vill göra baserat på en begäran om talade naturligt språk

Tal Text-API: et stöder interaktiv tal skrivfel med realtid kontinuerlig recognition och mellanliggande resultat. Det stöder också slutet av tal identifiering, valfria automatisk versaler och skiljetecken, svordomar Maskning och text normalisering.

Du kan anpassa tal till Text, ljud och språk modeller för särskilda ordförråd, störningar miljöer och olika sätt att tala.

## <a name="text-to-speech"></a>Text till tal

Den [Text till tal](text-to-speech.md) (TTS) eller tal sammanfattande API konverterar oformaterad text till låta naturliga tal, levereras till ditt program i en ljudfil. Flera röster, varierande i kön eller dialekt är tillgängliga för många språk som stöds.

API: et stöder tal sammanfattande Markup Language (SSML) taggar, så att du kan ange exakta Fonetiskt uttal för program som krånglar ord. SSML kan också ange egenskaper för tal (inklusive betoning, hastighet, volym, kön och breddsteg) direkt i texten.

Nedan följer vanliga användningsområden för Text till tal-API.

* Talutdata som ett alternativt utdata för nedsatt användare
* Fråga efter bilen program, till exempel navigering röst
* Vardagliga samtalsuttryck användargränssnitt tillsammans med tal till Text-API

Om du behöver en dialekt som inte stöds eller bara vill ha en unik röst för ditt program, stöder Text till tal-API: et anpassade röst modeller.

## <a name="speech-translation"></a>Talöversättning

Den [tal översättning](speech-translation.md) API kan användas antingen för att översätta strömmande ljud i nära realtid eller för att behandla inspelade tal. Med strömmande översättning returnerar tjänsten mellanliggande resultat som kan visas för användaren att ange översättning förlopp. Resultat kan returneras som text eller som röst.

Användningsområden för översättning tal inkluderar följande.

* Implementera en ”vardagliga samtalsuttryck” översättning mobila app eller enhet för resenärer 
* Ange automatiska översättningar för textning av ljud-och

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Med introduktionen av tjänsten enhetlig tal, Microsoft och dess samarbetspartner erbjuder en plattform för integrerad maskin-och programvara som optimerats för att utveckla tal-aktiverade enheter: den [tal enheter SDK](speech-devices-sdk.md). Detta SDK lämpar sig för att utveckla smarta tal enheter för alla typer av program.

Tal enheter SDK kan du skapa egna omgivande enheter med ett anpassat wake ord, så att stack som utlöser ljud avbildning är unik för ditt varumärke. Det ger också överlägsen ljud bearbetning från flera kanaler källor för exaktare taligenkänning, inklusive brus Undertryckning, långt fältet röst- och beamforming.

## <a name="next-steps"></a>Nästa steg

Hämta en kostnadsfri utvärderingsprenumeration nyckel för tjänsten tal.

> [!div class="nextstepaction"]
> [Prova tal tjänsten gratis](get-started.md)
