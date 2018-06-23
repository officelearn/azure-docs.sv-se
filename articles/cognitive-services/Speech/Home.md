---
title: Tjänsten Microsoft Speech | Microsoft Docs
description: Använda Microsoft tal-API för att lägga till tal-driven åtgärder i dina appar, inklusive realtid interaktion med användare.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: c041132e992f07e94e4b6669ec7ce174f7c2d0dd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352206"
---
# <a name="microsoft-speech-api-overview"></a>Översikt över Microsoft tal-API

Molnbaserad Microsoft Speech API ger utvecklare ett enkelt sätt att skapa kraftfulla tal-aktiverade funktioner i sina program som röst-kommandokontroll dialogruta för användare med hjälp av naturliga tal konversation och tal skrivfel och dictation. Microsofts Speech API stöder både *tal till Text* och *Text till tal* konvertering.

- **Tal till Text** API konverterar tal till text som kan användas som indata eller kommandon för att kontrollera ditt program.
- **Text till tal** API konverterar text till ljudströmmar kan spelas upp till användare av ditt program.

## <a name="speech-to-text-speech-recognition"></a>Tal till text (Taligenkänning)

Microsoft taligenkänning API *transcribes* ljudströmmar till text som programmet kan visa för användaren eller vidta åtgärder som kommandot indata. Det finns två sätt för utvecklare att lägga till tal i sina appar: REST API: er **eller** Websocket-baserade klientbibliotek.

- [REST API: erna](GetStarted/GetStartedREST.md): utvecklare kan använda HTTP-anrop från sina appar till tjänsten för taligenkänning.
- [Klientbibliotek](GetStarted/GetStartedClientLibraries.md): för avancerade funktioner utvecklare kan hämta Microsoft Speech klientbibliotek och länka till sina appar.  Klientbiblioteken är tillgängliga på olika plattformar (Windows, Android, iOS) med hjälp av olika språk (C#, Java, JavaScript, ObjectiveC). Till skillnad från REST API: er använda klientbiblioteken Websocket-baserade procotol.

| Användningsfall | [REST API:er](GetStarted/GetStartedREST.md) | [Klientbibliotek](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Konvertera en kort tal, till exempel kommandon (ljud längd < 15 s) utan mellanliggande resultat | Ja | Ja |
| Konvertera en lång ljud (> 15 s) | Nej | Ja |
| Dataströmmen ljud med mellanliggande resultat som önskas | Nej | Ja |
| Förstå texten konverteras från ljud med THOMAS | Nej | Ja |

Oavsett vilken metod utvecklare välja (REST API: er eller klientbibliotek), Microsoft tal-tjänsten har stöd för följande:

- Avancerad speech recognition teknik från Microsoft som används av Cortana, Office Dictation, Office översättare och andra Microsoft-produkter.
- Realtid kontinuerlig Igenkännande. Taligenkänning API kan användare transkribera ljud till text i realtid och har stöd för att ta emot mellanresultat ord som hittills har tolkats. Tjänsten tal stöder också slutet av tal identifiering. Dessutom kan användare välja ytterligare funktioner för formatering, som versaler och skiljetecken, maskering svordomar och text normalisering.
- Har stöd för optimerad tal resultat för *interaktiva*, *konversation*, och *dictation* scenarier. För scenarier som kräver anpassade språk modeller och ljud, [anpassade tal tjänsten](../custom-speech-service/cognitive-services-custom-speech-home.md) kan du skapa tal modeller som är anpassade till dina program och dina användare.
- Stöd för flera språk för talade i flera dialekter. En fullständig lista över språk som stöds i varje recognition läge finns [språk](api-reference-rest/supportedlanguages.md).
- Integrering med en förståelse för språk. Förutom att konvertera indata ljud till text, den *tal till Text* tillhandahåller en ytterligare funktion att förstå vad det innebär att texten. Den använder den [språk förstå Intelligent Service(LUIS)](../LUIS/Home.md) att extrahera avsikter och enheter från den tolkade texten.

### <a name="next-steps"></a>Nästa steg

- Komma igång med att använda Microsoft speech recognition tjänsten med [REST API: er](GetStarted/GetStartedREST.md) eller [klientbiblioteken](GetStarted/GetStartedClientLibraries.md).
- Checka ut [programexempel](samples.md) i din önskade programmeringsspråket.
- Gå till avsnittet referens för att hitta [Microsoft tal Protocol](API-Reference-REST/websocketprotocol.md) information och referenser för API: et.

## <a name="text-to-speech-speech-synthesis"></a>Text till tal (tal sammanfattande)

*Text till tal* API: er används REST konvertera strukturerade text till en ljudström. API: er ger snabb text till tal-konvertering i olika röster och språk. Användare har också möjlighet att ändra ljud egenskaper som uttal, volym, breddsteg osv. med hjälp av SSML taggar.

### <a name="next-steps"></a>Nästa steg

- Komma igång med att använda Microsoft text till tal-tjänst: [Text till tal-API-referens](api-reference-rest/bingvoiceoutput.md). En fullständig lista över språk och röster som stöds av Text till tal, se [språk som stöds och röst teckensnitt](api-reference-rest/bingvoiceoutput.md#SupLocales).
