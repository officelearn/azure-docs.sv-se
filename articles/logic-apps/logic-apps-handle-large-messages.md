---
title: Hantera stora meddelanden – Azure Logic Apps | Microsoft Docs
description: Lär dig att hantera stora meddelanden storlekar med storlekar i Azure Logic Apps
services: logic-apps
documentationcenter: ''
author: shae-hurst
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 4/27/2018
ms.author: shhurst
ms.openlocfilehash: e6ac6a4aa46feb768df437ff9d5969b2b41092c3
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041653"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Hantera stora meddelanden med storlekar i Azure Logic Apps

Vid hantering av meddelanden, begränsar Logic Apps meddelandeinnehåll till en maximal storlek. Den här gränsen som hjälper dig att minska effekterna som skapats av lagring och bearbetning av stora meddelanden. Om du vill hantera meddelanden som är större än den här gränsen, Logic Apps kan *segment* ett stort meddelande i mindre meddelanden. På så sätt kan du fortfarande överföra stora filer med hjälp av Logic Apps vissa villkor. Vid kommunikation med andra tjänster via anslutningar eller HTTP, Logic Apps kan förbruka stora meddelanden men *endast* i segment. Det här tillståndet innebär kopplingar måste också ha stöd för storlekar eller underliggande HTTP-meddelandeutbyte mellan Logikappar och dessa tjänster måste använda storlekar.

Den här artikeln visar hur du ställer in storlekar stöd för meddelanden som är större än gränsen.

## <a name="what-makes-messages-large"></a>Vad gör meddelanden ”stora”?

Meddelanden är ”stora” baserat på den tjänst som hanterar dessa meddelanden. Exakta storleksgränsen för stora meddelanden skiljer sig åt mellan Logic Apps och anslutningsapparna. Både Logic Apps och anslutningsapparna kan inte använda stora meddelanden som måste vara chunked direkt. Storleksgräns för Logic Apps-meddelande Se [Logic Apps gränser och konfigurering](../logic-apps/logic-apps-limits-and-config.md).
Storleksgräns för varje koppling meddelande finns den [kopplingens viss teknisk information](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Chunked-meddelandehantering för Logic Apps

Logic Apps kan inte direkt använda utdata från segmenterat meddelanden som är större än storleksgränsen för meddelanden. Endast de åtgärder som har stöd för storlekar kan komma åt meddelandeinnehåll i dessa utdata. Därför en åtgärd som hanterar stora meddelanden måste uppfylla *antingen* dessa kriterier:

* Har inbyggt stöd för storlekar när åtgärden tillhör en koppling. 
* Ha storlekar support som är aktiverad i den åtgärden runtime-konfigurationen. 

Annars kan få du ett körningsfel inträffade när du försöker få åtkomst till stora innehåll utdata. För att aktivera storlekar, se [konfigurera storlekar support](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Chunked-meddelandehantering för kopplingar

Tjänster som kommunicerar med Logic Apps kan ha sina egna begränsningar för meddelandestorlek. Dessa gränser är ofta mindre än gränsen för Logic Apps. Till exempel om vi antar att en anslutning har stöd för storlekar, en anslutningsapp kan överväga att meddelandet 30 MB så stor, men inte av Logic Apps. Om du vill följa den här kopplingen gränsen Logic Apps delar upp några meddelanden större än 30 MB i mindre segment.

Underliggande för att maximera dedupliceringsbesparingen protokollet är osynliga för slutanvändare för kopplingar som har stöd för storlekar. Inte alla kopplingar stöder dock storlekar, och dessa kopplingar generera körningsfel när inkommande meddelanden överskrider de kopplingar storleksgränser.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurera storlekar över HTTP

I den allmänna http-scenarier, du kan dela upp stora nedladdning av innehåll och överför via HTTP, så att din logikapp och en slutpunkt kan utbyta stora meddelanden. Dock måste du dela upp meddelanden på det sätt som förväntar sig att Logic Apps. 

Om en slutpunkt har aktiverat storlekar för nedladdningar eller överföringar, dela stora meddelanden automatiskt in HTTP-åtgärder i din logikapp. I annat fall måste du ställa in storlekar support på slutpunkten. Om du inte äger och hanterar endpoint eller anslutningen, kanske du inte har möjligheten att konfigurera storlekar.

Även om en HTTP-åtgärd inte redan aktiverar storlekar kan du också ställa in storlekar i åtgärdens `runTimeConfiguration` egenskapen. Du kan ange den här egenskapen i åtgärden, direkt i Kodredigeraren i vyn som beskrivs senare eller i Logic Apps Designer enligt nedan:

1. I HTTP-åtgärden övre högra hörnet väljer du knappen med tre punkter (**...** ), och välj sedan **inställningar**.

   ![Öppna inställningsmenyn på åtgärden,](./media/logic-apps-handle-large-messages/http-settings.png)

2. Under **innehållsöverföring**anger **Tillåt storlekar** till **på**.

   ![Aktivera storlekar](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Om du vill fortsätta konfigurera storlekar för nedladdningar eller överföringar, fortsätter du med följande avsnitt.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Ladda ned innehåll i segment

Många slutpunkter Skicka automatiskt stora meddelanden i segment laddas ned via en HTTP GET-begäran. Hämta segmenterat meddelanden från en slutpunkt över HTTP genom slutpunkten måste ha stöd för partiell innehållsbegäranden eller *chunked-nedladdningar*. När logikappen skickar en HTTP GET-begäran till en slutpunkt för att ladda ned innehåll och slutpunkten svarar med statuskoden ”206”, innehåller svaret segmenterat innehåll. Logic Apps kan inte kontrollera om en slutpunkt stöder partiella begäranden. Men när logikappen får först ”206”-svar kan skickar logikappen automatiskt flera begäranden om att hämta allt innehåll.

Om du vill kontrollera om en slutpunkt kan ha stöd för partiellt innehåll kan skicka en HEAD-begäran. Den här begäran hjälper dig att avgöra om svaret innehåller den `Accept-Ranges` rubrik. På så sätt kan om slutpunkten stöder segmenterat nedladdningar men inte skickar segmenterade innehåll, kan du *föreslår* det här alternativet genom att ange den `Range` rubriken i HTTP GET-begäran. 

De här stegen beskriver detaljerad processen Logic Apps använder för att hämta segmenterat innehållet från en slutpunkt i logikappen:

1. Logikappen skickar en HTTP GET-begäran till slutpunkten.

   Rubriken kan du också inkludera en `Range` fält som beskriver en byte-intervallet för att begära innehåll segment.

2. Slutpunkten som svarar med ”206” statuskoden och en meddelandetext för HTTP.

    Information om innehållet i det här segmentet visas i svarets `Content-Range` -huvud, inklusive information som hjälper Logikappar bestämma start- och slutdatum för segmentet, plus den totala storleken på hela innehållet innan storlekar.

3. Logikappen skickar automatiskt Uppföljnings HTTP GET-begäranden.

    Logikappen skickar Uppföljnings GET-begäranden tills hela innehållet hämtas.

Den här åtgärdsdefinitionen visar exempelvis en HTTP GET-begäran som anger den `Range` rubrik. Rubriken *föreslår* att slutpunkten bör svara med chunked-innehåll:

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

GET-begäran anger rubriken ”Range” till ”byte = 0 1023”, vilket är en uppsättning byte. Om slutpunkten stöder begäranden för del av innehåll, svarar slutpunkten med ett content segment från det begärda intervallet. Baserat på slutpunkten, det exakta formatet för fältet ”Range” huvud kan skilja sig åt.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Ladda upp innehåll i segment

Om du vill överföra segmenterat innehåll från en HTTP-åtgärd, åtgärden måste ha aktiverat för att maximera dedupliceringsbesparingen support via åtgärdens `runtimeConfiguration` egenskapen. Den här inställningen gör det möjligt för åtgärden som ska starta protokollet för att maximera dedupliceringsbesparingen. Din logikapp kan sedan skicka en inledande post- eller PUT-meddelande till mål-slutpunkten. När slutpunkten svarar med föreslagna segmentstorleken, följer logikappen genom att skicka HTTP KORRIGERA förfrågningar som innehåller innehåll segment.

De här stegen beskriver detaljerad processen Logic Apps använder för att ladda upp segmenterat innehåll från din logikapp till en slutpunkt:

1. Logikappen skickar en inledande HTTP post- eller PUT-begäran med tom meddelandetext. Huvudet för begäran och innehåller informationen om det innehåll som din logikapp vill ladda upp i segment:

   | Logic Apps begära huvud-fält | Värde | Typ | Beskrivning |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-läge** | segmentvis | Sträng | Anger att innehållet har laddats upp i segment |
   | **x-ms-content-length** | <*innehållslängd*> | Integer | Hela innehållets storlek i byte innan storlekar |
   ||||

2. Slutpunkten som svarar med ”200” lyckades statuskod och den här valfria informationen:

   | Slutpunkten svarsfältet rubrik | Typ | Krävs | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **x-ms-segment-storlek** | Integer | Nej | Den föreslagna segmentstorleken i byte |
   | **Plats** | Sträng | Nej | URL-plats att skicka HTTP-PATCH-meddelanden |
   ||||

3. Logikappen skapar och skickar Uppföljnings HTTP KORRIGERA meddelanden – var och en med den här informationen:

   * Ett content segment utifrån **x-ms--segmentstorleken** eller vissa internt beräknade storlek tills alla de innehåll addera **x-ms-content-length** sekventiellt har laddats upp

   * Följande rubrik information om det innehåll segmentet som skickas i varje PATCH-meddelande:

     | Logic Apps begära huvud-fält | Värde | Typ | Beskrivning |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*Adressintervall*> | Sträng | Byte-intervallet för det aktuella innehåll segmentet, inklusive Startvärde, slutar värde och den totala innehållsstorleken, till exempel ”: byte = 0-1023/10100” |
     | **Innehållstyp** | <*innehållstyp*> | Sträng | Typ av segmenterat innehåll |
     | **Content-Length** | <*innehållslängd*> | Sträng | Längden på storlek i byte för det aktuella segmentet |
     |||||

4. Efter varje PATCH-begäran bekräftar slutpunkten kvitto på varje segment genom att svara med statuskod ”200”.

Den här åtgärdsdefinitionen visar exempelvis en HTTP POST-begäran för att ladda upp segmenterat innehåll till en slutpunkt. I åtgärdens `runTimeConfiguration` egenskapen den `contentTransfer` egenskapsuppsättningar `transferMode` till `chunked`:

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