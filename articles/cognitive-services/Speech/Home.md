---
title: Microsoft Taligenkänning i Bing-tjänsten | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Använd Microsoft Speech API för att lägga till tal drivna åtgärder i dina appar, inklusive interaktion i real tid med användare.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966652"
---
# <a name="what-is-bing-speech"></a>Vad är Taligenkänning i Bing?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Den molnbaserade Microsoft API för Bing-taligenkänning ger utvecklare ett enkelt sätt att skapa kraftfulla aktiverade funktioner i sina program, t. ex. röst kommando kontroll, användar dialog med naturliga tal konversationer och tal avskrift och diktering. Microsoft Speech API stöder både *tal till text* och *text till tal* Conversion.

- **Tal till text** API konverterar mänskligt tal till text som kan användas som ininformation eller kommandon för att styra ditt program.
- **Text till tal** API konverterar text till ljud strömmar som kan spelas upp till användaren av ditt program.

## <a name="speech-to-text-speech-recognition"></a>Tal till text (tal igenkänning)

Microsoft tal igenkännings- *API: et* beskrivar ljud strömmar till text som programmet kan visa för användaren eller agerar vid indata. Det finns två sätt för utvecklare att lägga till tal i sina appar: REST-API: er **eller** WebSocket-baserade klient bibliotek.

- [REST-API: er](GetStarted/GetStartedREST.md): Utvecklare kan använda HTTP-anrop från sina appar till tjänsten för tal igenkänning.
- [Klient bibliotek](GetStarted/GetStartedClientLibraries.md): För avancerade funktioner kan utvecklare Hämta Microsofts tal klient bibliotek och länka till sina appar.  Klient biblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med olika språk (C#, Java, Java Script, ObjectiveC). Till skillnad från REST API: erna använder klient biblioteken WebSocket-baserat protokoll.

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klient bibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera ett kort talade ljud, till exempel kommandon (ljud längd < 15 s) utan interimistiska resultat | Ja | Ja |
| Konvertera ett långt ljud (> 15 s) | Nej | Ja |
| Strömma ljud med önskade interimistiska resultat | Nej | Ja |
| Förstå texten som konverteras från ljud med LUIS | Nej | Ja |

Oavsett vilken metod utvecklare väljer (REST-API: er eller klient bibliotek) stöder Microsoft Speech service följande:

- Avancerad tal igenkännings teknik från Microsoft som används av Cortana, Office-diktering, Office Translator och andra Microsoft-produkter.
- I realtid kontinuerlig erkännande. Tal igenkännings-API: et gör det möjligt för användare att skriva in ljud i text i real tid och har stöd för att ta emot de mellanliggande resultaten av de ord som har identifierats hittills. Tal tjänsten stöder även identifiering av slut punkt i tal. Dessutom kan användarna välja ytterligare formateringsfunktioner, t. ex. kapitalisering och interpunktion, maskering av svordomar och text normalisering.
- Stöder optimerade tal igenkännings resultat för *interaktiva*, *konversations*-och *dikterings* scenarier. För användar scenarier som kräver anpassade språk modeller och akustiska modeller kan du [Custom Speech service](../custom-speech-service/cognitive-services-custom-speech-home.md) skapa tal modeller som är anpassade efter ditt program och dina användare.
- Stöd för många talade språk i flera dialekter. En fullständig lista över språk som stöds i varje tolknings läge finns i [tolknings språk](api-reference-rest/supportedlanguages.md).
- Integrering med språk förståelse. Förutom att konvertera indata-ljudet till text ger *tal till text* program en ytterligare möjlighet att förstå vad texten innebär. Den använder [language Understanding intelligent service (Luis)](../LUIS/what-is-luis.md) för att extrahera avsikter och entiteter från den tolkade texten.

### <a name="next-steps"></a>Nästa steg

- Kom igång med att använda Microsoft tal igenkännings tjänsten med [REST-API: er](GetStarted/GetStartedREST.md) eller [klient bibliotek](GetStarted/GetStartedClientLibraries.md).
- Kolla in [exempel program](samples.md) i önskat programmeringsspråk.
- Gå till avsnittet referens för att hitta information om [Microsoft Speech Protocol](API-Reference-REST/websocketprotocol.md) och API-referenser.

## <a name="text-to-speech-speech-synthesis"></a>Text till tal (tal syntes)

*Text till tal* API: er använder REST för att konvertera strukturerad text till en ljud ström. API: erna ger snabb text till tal konvertering i olika röster och språk. Dessutom kan användare även ändra ljud egenskaper som uttal, volym, färgdjup osv. använda SSML-taggar.

### <a name="next-steps"></a>Nästa steg

- Kom igång med att använda Microsoft text till tal service: [Text till tal-API referens](api-reference-rest/bingvoiceoutput.md). En fullständig lista över språk och röster som stöds av Text till tal finns i [nationella inställningar och röst teckensnitt som stöds](api-reference-rest/bingvoiceoutput.md#SupLocales).
