---
title: Vad är taltjänst (förhandsversion)? | Microsoft Docs
description: 'Tjänsten tal, en del av Microsofts Cognitive Services och unites flera Azure taltjänster som tidigare var tillgängliga separat: Bing-tal (som består av taligenkänning och text till tal), anpassat tal och Talöversättning.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: adfc854fc24b9e285c405f3038a21ec84cd2f4c2
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989343"
---
# <a name="what-is-the-speech-service-preview"></a>Vad är taltjänst (förhandsversion)?

Tjänsten tal, en del av Microsofts Cognitive Services och unites flera Azure taltjänster som tidigare var tillgängliga separat: Bing-tal (som består av taligenkänning och text till tal), anpassat tal och Talöversättning. Som dess ämnen drivs Speech-tjänsten av de tekniker som används i andra Microsoftprodukter, inklusive Cortana och Microsoft Office.

> [!NOTE]
> Speech-tjänsten är för närvarande i offentlig förhandsversion. Gå tillbaka här regelbundet efter uppdateringar till dokumentation och ytterligare exempel.

Med en prenumeration ger enhetliga Speech-tjänsten utvecklare ett enkelt sätt att ge sina program kraftfulla talbaserade funktioner. Dina appar kan nu funktionen voice-kommandot, transkription, diktering, talsyntes och översättning.

|Funktion|Beskrivning|
|-|-|
|Tal till text|Konverterar kontinuerlig tal till text som kan användas som indata för ditt program. Kan integreras med den [tjänst för Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikt från yttranden.|
|Text till tal|: Texten omvandlas till ljudfiler naturligt standardrösttyper syntetiserade tal.|
|Tal&nbsp;översättning|Ge översättningar av tal till andra språk, text eller tal utdata.|

## <a name="using-the-speech-service"></a>Med Speech-tjänsten

Speech-tjänsten är tillgänglig på två sätt. [SDK: N](speech-sdk.md) avlägsnar information om nätverksprotokoll. Den [REST API](rest-apis.md) fungerar med alla programmeringsspråk, men inte tillhandahåller de funktioner som erbjuds av SDK: N.

|<br>Metod|Tal<br>till Text|Text som ska<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[SDK: er](speech-sdk.md)|Ja|Nej|Ja|Bibliotek för specifika programmeringsspråk som förenklar utveckling.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i ditt program.|

## <a name="speech-to-text"></a>Tal till text

Den [tal till Text](speech-to-text.md) (STT) eller taligenkänning, API transkriberar ljud till text som ditt program kan acceptera som indata. Sedan kan programmet till exempel ange texten i ett dokument eller agera utifrån den som ett kommando.

Tal till Text separat har optimerats för interaktiva, konversationen och diktering scenarier. Här följer några vanliga användningsområden för tal till Text-API. 

* Identifiera en kort uttryck, till exempel ett kommando utan mellanliggande resultat
* Transkribera långa, inspelade uttryck, till exempel ett röstmeddelande
* Transkribera strömmande tal i realtid med ofullständiga resultat för diktering
* Ta reda på vad användare vill göra baserat på en begäran om talat naturligt språk

Tal till Text-API har stöd för interaktiva taltranskription med igenkänning av kontinuerlig-i realtid och mellanliggande resultat. Det finns även stöd för identifiering av talslut, valfri automatisk ändring till versal och skiljetecken, svordomsmaskning och textnormalisering.

Du kan anpassa tal till Text akustiska och språkmodeller för att anpassa specialiserade ordförråd bort störande miljöer och flera olika sätt att tala.

## <a name="text-to-speech"></a>Text till tal

Den [Text till tal](text-to-speech.md) (text till tal), eller talsyntes, API konverterar oformaterad text till tal för naturlig standardrösttyper, levereras till ditt program i en ljudfil. Flera röster, varierande i kön eller accent, är tillgängliga för många språk som stöds.

API: et stöder [tal syntes Markup Language (SSML)](speech-synthesis-markup.md) taggar, så att du kan ange exakta Fonetiskt uttal för problematiska orden. SSML kan även ange talegenskaper (inklusive betoning, hastighet, volym, kön och tonläge) direkt i texten.

Här följer några vanliga användningsområden för Text till tal-API.

* Tal-utdata som alternativa skärmutdata för användare med nedsatt syn
* Röst fråga om bilar program som t.ex navigering
* Konversationsanpassad användargränssnitt tillsammans med tal till Text-API

Om du behöver en dialekt som inte stöds eller bara vill ha en unik röst för ditt program, Text till tal-API stöder [anpassade röst modeller](how-to-customize-voice-font.md).

## <a name="speech-translation"></a>Talöversättning

Den [Talöversättning](speech-translation.md) API kan användas antingen för att översätta strömmande ljud i nära realtid eller för att behandla inspelade tal. Vid direktuppspelning translation returnerar tjänsten mellanliggande resultat som kan visas för användaren att ange translation förlopp. Resultaten kan returneras som text eller som röst.

Användningsområden för Talöversättning är följande.

* Implementera en ”konversationsanpassade” translation mobila app eller enhet för resenärer 
* Ange automatiska översättningar för textning av ljud- och inspelningar

## <a name="speech-devices-sdk"></a>Speech Devices SDK

Med introduktionen av enhetliga Speech-tjänsten, Microsoft och dess samarbetspartner erbjuder en plattform för integrerad maskin-och programvara som optimerats för att utveckla tal-aktiverade enheter: den [tal Devices SDK](speech-devices-sdk.md). Detta SDK lämpar sig för utveckling av smarta talenheter för alla typer av program.

Tal Devices SDK kan du skapa egna omgivande enheter med ett ord på anpassade wake, så att stack-ikonen som utlöser ljud avbildning är unikt för ditt varumärke. Det ger också överlägsen ljudbearbetning från källor med flera kanaler för bättre taligenkänning, inklusive bullerdämpning, långdistansidentifiering och beamforming.

SDK: N är baserad på webbsockets via port 443.

## <a name="next-steps"></a>Nästa steg

Få en kostnadsfri utvärderingsprenumeration nyckel för Speech-tjänsten.

> [!div class="nextstepaction"]
> [Prova Speech-tjänsten utan kostnad](get-started.md)
