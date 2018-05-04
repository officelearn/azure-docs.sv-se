---
title: Hantera stora meddelanden i Azure Logic Apps | Microsoft Docs
description: Lär dig att hantera stora meddelandestorleken med högoptimerat i logikappar
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: SyntaxC4
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 4/27/2018
ms.author: shhurst; LADocs
ms.openlocfilehash: 421a207456908fa3b10582c2287b1b2467ff74b1
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2018
---
# <a name="handle-large-messages-with-chunking-in-logic-apps"></a>Hantera stora meddelanden med högoptimerat i Logic Apps

När du hanterar meddelanden begränsar Logic Apps meddelandeinnehåll till en maximal storlek.
Den här gränsen som bidrar till att minska omkostnader som skapats av lagring och bearbetning av stora meddelanden.
Om du vill hantera meddelanden som är större än den här gränsen Logic Apps kan *segment* ett stort meddelande i mindre meddelanden. På så sätt kan du fortfarande överföra stora filer med Logic Apps vissa villkor.
När du kommunicerar med andra tjänster med hjälp av kopplingar eller HTTP Logic Apps kan förbruka stora meddelanden men *endast* i segment.
Detta innebär kopplingar måste också ha stöd för högoptimerat eller underliggande HTTP-meddelande utbytet mellan Logic Apps och dessa tjänster måste använda högoptimerat.

Den här artikeln visar hur du ställer in högoptimerat stöd för hantering av meddelanden som överskrider gränsen.

## <a name="what-makes-messages-large"></a>Vad gör meddelanden ”stora”?

Meddelanden är ”stora” baserat på den tjänst som hanterar dessa meddelanden.
Den exakta storleksgränsen på stora meddelanden skiljer sig åt mellan Logic Apps och kopplingar.
Både Logic Apps och kopplingar kan inte direkt förbruka stora-meddelanden måste vara chunked. Storleksgräns för Logic Apps meddelande finns [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md).
Storleksgräns för varje koppling meddelande finns i [kopplingens specifika tekniska detaljer](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Chunked-meddelandehantering för Logic Apps

Logic Apps kan inte direkt använda utdata från chunked-meddelanden som överskrider storleksgränsen för Logic Apps-meddelande.
Åtgärder som har stöd för högoptimerat kan komma åt innehållet i dessa utdata.
Därför måste en åtgärd som hanterar stora meddelanden *antingen*:

* Inbyggt stöd för högoptimerat när åtgärden tillhör en koppling.
* Har högoptimerat aktiverat i konfigurationen för den åtgärden runtime-stöd.

Annars får ett körningsfel när du försöker komma åt stora innehåll utdata.
För att aktivera högoptimerat, se [konfigurera högoptimerat support](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Chunked-meddelandehantering för kopplingar

Tjänster som kommunicerar med Logic Apps kan ha sina egna storleksgränser för meddelanden.
Dessa gränser är ofta mindre än gränsen som Logic Apps. Till exempel förutsatt att en anslutning har stöd för högoptimerat kan överväga en koppling meddelandet 30 MB så stor, men inte av Logic Apps.
Om du vill följa den här anslutningen gränsen delar Logic Apps alla meddelanden större än 30 MB i mindre delar.

Underliggande segment protokollet är osynliga för slutanvändare för kopplingar som har stöd för högoptimerat.
Inte alla kopplingar stöder dock högoptimerat, och dessa kopplingar generera körningsfel när inkommande meddelanden överskrider storleksgränsen för de kopplingar.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Ställ in högoptimerat via HTTP

I den allmänna http-scenarier kan dela upp stora nedladdning av innehåll och överförs via HTTP, så att din logikapp och en slutpunkt kan utbyta stora meddelanden. Dock måste du dela in meddelanden på sätt som förväntar att Logic Apps.

En slutpunkt har aktiverat högoptimerat för hämtningar eller överföringar, dela HTTP-åtgärder i din logikapp automatiskt in stora meddelanden. I annat fall måste du ställa in högoptimerat stöd på slutpunkten. Om du inte äger och hanterar endpoint eller koppling, kanske inte alternativet för att ställa in högoptimerat.

Även om en HTTP-åtgärd inte redan aktiverar högoptimerat, du måste också ange högoptimerat i åtgärden `runTimeConfiguration` egenskapen.
Du kan ange den här egenskapen i åtgärden, antingen direkt i koden visa redigeraren såsom beskrivs senare eller i Logic Apps Designer som beskrivs här:

1. Högerklicka på puknappen i HTTP-åtgärden övre högra hörnet (**...** ), och välj **inställningar**.

2. Under **innehållsöverföring**, ange **Tillåt högoptimerat** till **på**.

3. Om du vill fortsätta konfigurera högoptimerat för hämtningar eller överföringar, fortsätter du med följande avsnitt.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Hämta innehåll i segment

Många slutpunkter Skicka automatiskt stora meddelanden i segment om du har hämtat via en HTTP GET-begäran.
Hämta chunked meddelanden från en slutpunkt över HTTP genom slutpunkten måste stödja partiell innehållsbegäranden eller *chunked hämtningar*. När logikappen skickar en HTTP GET-begäran till en slutpunkt för hämtning av innehåll och slutpunkten svarar med en ”206” statuskod, innehåller svaret chunked innehåll.
Logic Apps kan inte kontrollera om en slutpunkt stöder partiella begäranden.
Men när logikappen hämtar först ”206” svaret, skickar logikappen automatiskt flera begäranden att hämta allt innehåll.

Skicka en HEAD-begäran för att kontrollera om en slutpunkt stöder del av innehåll. Denna begäran hjälper dig att avgöra om svaret innehåller den `Accept-Ranges` rubrik.
På så sätt kan om slutpunkten stöder chunked hämtningar men inte skickar chunked innehåll, kan du *föreslår* det här alternativet genom att ange den `Range` rubriken i HTTP GET-begäran.

Dessa steg beskriver detaljerad process Logic Apps används för att hämta chunked innehåll från en slutpunkt till din logikapp:

1. Din logikapp skickar en HTTP GET-begäran till slutpunkten.

   Begärandehuvudet kan du också inkludera en `Range` fält som beskriver en byte-intervall för att begära innehåll segment.

2. Slutpunkten svarar med ”206” statuskoden och en HTTP-meddelandetexten.

    Information om innehållet i det här segmentet visas i svaret `Content-Range` huvud, inklusive information som hjälper Logic Apps fastställa start- och slutdatum för segmentet, plus den totala storleken på hela innehållet innan högoptimerat.

3. Din logikapp skickar automatiskt uppföljning HTTP GET-begäranden.

    Din logikapp skickar uppföljning GET-begäranden tills hela innehållet hämtas.

Åtgärdsdefinitionen för denna visar exempelvis en HTTP GET-begäran som anger den `Range` huvudet *tyder på* att slutpunkten svara med chunked innehåll:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Begäran om att hämta anger rubriken ”intervallet” till ”byte = 0 1023”, vilket är antalet byte. Om slutpunkten stöder begäranden för en del av innehåll, svarar slutpunkten med ett innehåll segment från det begärda intervallet.
Baserat på slutpunkten kan exakt format för fältet ”intervall” huvudet variera.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Överföra innehåll i segment

Om du vill överföra chunked innehåll från en HTTP-åtgärd måste åtgärden aktiverat segment stöd för åtgärden `runtimeConfiguration` egenskapen.
Den här inställningen tillåter åtgärden för att starta protokollet segment.
Din logikapp kan sedan skicka ett inledande POST eller PUT-meddelande till mål-slutpunkten.
När slutpunkten svarar med en föreslagna segmentstorleken måste följer din logikapp genom att skicka korrigering av HTTP-begäranden som innehåller innehåll segment.

Dessa steg beskriver detaljerad process Logic Apps använder för överföring av chunked innehåll från din logikapp till en slutpunkt:

1. Din logikapp skickar en inledande HTTP POST eller PUT-begäran med ett tomt meddelandetexten. Begärandehuvudet innehåller följande information om det innehåll som din logikapp vill överföra i segment:

   | Logic Apps begära huvudfältet | Värde | Typ | Beskrivning |
   |---------------------------------|-------|------|-------------|
   | **x-ms-överföring-läge** | Chunked | Sträng | Anger att innehållet har laddats upp i segment |
   | **x-ms-content-length** | <*innehållslängden*> | Integer | Hela innehållets storlek i byte innan högoptimerat |
   ||||

2. Slutpunkten svarar med statuskod ”200” lyckades och den här valfria informationen:

   | Slutpunkten svar huvudfältet | Typ | Krävs | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **x-ms--segmentstorleken** | Integer | Nej | Föreslagna segmentstorleken i byte |
   | **Plats** | Sträng | Nej | URL-plats var du vill skicka meddelanden för HTTP-korrigering |
   ||||

3. Din logikapp skapar och skickar uppföljning meddelanden i HTTP-korrigering: med den här informationen:

   * Ett innehåll segment baserat på **x-ms--segmentstorleken** eller vissa internt beräknade storlek tills alla de innehåll summering **x-ms-content-length** överförs sekventiellt

   * Dessa huvudet innehåller information om innehåll segment skickas i varje korrigering meddelande:

     | Logic Apps begära huvudfältet | Värde | Typ | Beskrivning |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*intervallet*> | Sträng | Byte-intervallet för det aktuella innehållet segmentet, inklusive startvärdet slutar värde och den totala storleken för innehåll, till exempel ”: byte = 0-1023/10100” |
     | **Innehållstyp** | <*innehållstyp*> | Sträng | Typ av chunked innehåll |
     | **Content-Length** | <*innehållslängden*> | Sträng | Längden på storlek i byte för det aktuella segmentet |
     |||||

4. Efter varje PATCH-begäran bekräftar slutpunkten inleverans för varje segment av svarar med ”200” statuskod.

Den här åtgärdsdefinitionen visar exempelvis en HTTP POST-begäran för överföring av chunked innehåll till en slutpunkt.
I åtgärden `runTimeConfiguration` -egenskapen i `contentTransfer` egenskapsuppsättningar `transferMode` till `chunked`:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```