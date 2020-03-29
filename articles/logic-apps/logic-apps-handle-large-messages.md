---
title: Hantera stora meddelanden med hjälp av segmentering
description: Lär dig hur du hanterar stora meddelandestorlekar genom att använda segmentering i automatiserade uppgifter och arbetsflöden som du skapar med Azure Logic Apps
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456565"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Hantera stora meddelanden med segmentering i Azure Logic Apps

När du hanterar meddelanden begränsar Logic Apps meddelandeinnehåll till en maximal storlek. Den här gränsen bidrar till att minska omkostnaderna som skapas genom att lagra och bearbeta stora meddelanden. Om du vill hantera meddelanden som är större än den här gränsen kan Logic Apps *segmenta* ett stort meddelande i mindre meddelanden. På så sätt kan du fortfarande överföra stora filer med Logic Apps under särskilda förhållanden. När du kommunicerar med andra tjänster via anslutningsappar eller HTTP kan Logic Apps använda stora meddelanden men *bara* i segment. Det här villkoret innebär att kopplingar också måste stödja segmentering, eller så måste det underliggande HTTP-meddelandeutbytet mellan Logic Apps och dessa tjänster använda segmentering.

Den här artikeln visar hur du kan ställa in segmentering för åtgärder som hanterar meddelanden som är större än gränsen. Logic App-utlösare stöder inte segmentering på grund av den ökade omkostnaderna för utbyte av flera meddelanden. 

## <a name="what-makes-messages-large"></a>Vad gör meddelanden "stora"?

Meddelandena är "stora" baserat på den tjänst som hanterar dessa meddelanden. Den exakta storleksgränsen för stora meddelanden skiljer sig åt mellan Logic Apps och connectors. Både Logic Apps och connectors kan inte direkt använda stora meddelanden, som måste vara segmenterade. Storleksgränsen för Logic Apps finns i [Logic Apps gränser och konfiguration](../logic-apps/logic-apps-limits-and-config.md).
För varje anslutnings meddelandestorleksgräns läser du [kopplingens specifika tekniska information](../connectors/apis-list.md).

### <a name="chunked-message-handling-for-logic-apps"></a>Hantering av segmenterade meddelanden för Logic Apps

Logic Apps kan inte direkt använda utdata från segmenterade meddelanden som är större än gränsen för meddelandestorlek. Endast åtgärder som stöder segmentering kan komma åt meddelandeinnehållet i dessa utdata. En åtgärd som hanterar stora meddelanden måste därför uppfylla *antingen* dessa kriterier:

* Stöd för segmentering när åtgärden tillhör en koppling. 
* Ha segmenteringsstöd aktiverat i åtgärdens körningskonfiguration. 

Annars får du ett körningsfel när du försöker komma åt stora innehållsutdata. Om du vill aktivera segmentering finns i [Konfigurera stöd för segmentering](#set-up-chunking).

### <a name="chunked-message-handling-for-connectors"></a>Hantering av segmenterat meddelande för kopplingar

Tjänster som kommunicerar med Logic Apps kan ha sina egna gränser för meddelandestorlek. Dessa gränser är ofta mindre än logic apps-gränsen. Om du till exempel antar att en koppling stöder segmentering kan en koppling betrakta ett 30 MB-meddelande som stort, medan Logic Apps inte gör det. För att uppfylla den här anslutningsgränsen delar Logic Apps alla meddelanden som är större än 30 MB i mindre segment.

För kopplingar som stöder segmentering är det underliggande segmentningsprotokollet osynligt för slutanvändare. Alla kopplingar stöder dock inte segmentering, så dessa kopplingar genererar körningsfel när inkommande meddelanden överskrider anslutningsstorleksgränserna.

> [!NOTE]
> För åtgärder som använder segmentering kan du inte skicka utlösartexten eller använda uttryck som `@triggerBody()?['Content']` i dessa åtgärder. För text- eller JSON-filinnehåll kan du i stället prova att använda åtgärden [ **Komponera** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) eller [skapa en variabel](../logic-apps/logic-apps-create-variables-store-values.md) för att hantera innehållet. Om utlösartexten innehåller andra innehållstyper, till exempel mediefiler, måste du utföra andra steg för att hantera innehållet.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Konfigurera segmentering via HTTP

I allmänna HTTP-scenarier kan du dela upp stora innehållshämtningar och uppladdningar via HTTP, så att logikappen och en slutpunkt kan utbyta stora meddelanden. Du måste dock segment meddelanden på det sätt som Logic Apps förväntar sig. 

Om en slutpunkt har aktiverat segmentering för nedladdningar eller uppladdningar segmenterar HTTP-åtgärderna i logikappen automatiskt stora meddelanden. Annars måste du ställa in segmenteringsstöd på slutpunkten. Om du inte äger eller styr slutpunkten eller kopplingen kanske du inte har möjlighet att ställa in segmentering.

Om en HTTP-åtgärd inte redan aktiverar segmentering måste du också ställa `runTimeConfiguration` in segmentering i åtgärdens egenskap. Du kan ange den här egenskapen i åtgärden, antingen direkt i kodvyredigeraren enligt beskrivningen senare, eller i Logic Apps Designer enligt beskrivningen här:

1. I HTTP-åtgärdens övre högra hörn väljer du ellipsknappen (**...**) och väljer sedan **Inställningar**.

   ![Öppna inställningsmenyn på åtgärden](./media/logic-apps-handle-large-messages/http-settings.png)

2. Under **Innehållsöverföring**anger du **Tillåt segmentering** **till På**.

   ![Aktivera segmentering](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Om du vill fortsätta konfigurera segmentering för nedladdningar eller uppladdningar fortsätter du med följande avsnitt.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Ladda ned innehåll i segment

Många slutpunkter skickar automatiskt stora meddelanden i segment när de hämtas via en HTTP GET-begäran. Om du vill hämta segmenterade meddelanden från en slutpunkt via HTTP måste slutpunkten ha stöd för partiella innehållsbegäranden eller *segmenterade nedladdningar*. När logikappen skickar en HTTP GET-begäran till en slutpunkt för nedladdning av innehåll och slutpunkten svarar med statuskoden "206" innehåller svaret segmenterat innehåll. Logic Apps kan inte styra om en slutpunkt stöder partiella begäranden. Men när logikappen får det första "206"-svaret skickar logikappen automatiskt flera begäranden om att hämta allt innehåll.

Om du vill kontrollera om en slutpunkt kan stödja partiellt innehåll skickar du en HEAD-begäran. Den här begäran hjälper dig att `Accept-Ranges` avgöra om svaret innehåller huvudet. På så sätt, om slutpunkten stöder segmenterade nedladdningar men inte *suggest* skickar segmenterat `Range` innehåll, kan du föreslå det här alternativet genom att ange huvudet i http GET-begäran. 

De här stegen beskriver den detaljerade process som Logic Apps använder för att hämta segmenterat innehåll från en slutpunkt till logikappen:

1. Logikappen skickar en HTTP GET-begäran till slutpunkten.

   Begäranden kan eventuellt innehålla `Range` ett fält som beskriver ett byteintervall för att begära innehållssegment.

2. Slutpunkten svarar med statuskoden "206" och en HTTP-meddelandetext.

    Information om innehållet i det här segmentet visas i svarets `Content-Range` rubrik, inklusive information som hjälper Logic Apps att avgöra början och slutet för segmentet, plus den totala storleken på hela innehållet innan segmentering.

3. Logikappen skickar automatiskt uppföljnings-HTTP GET-begäranden.

    Din logikapp skickar uppföljningsbegäranden tills hela innehållet hämtas.

Den här åtgärdsdefinitionen visar till exempel `Range` en HTTP GET-begäran som anger huvudet. Rubriken *föreslår* att slutpunkten ska svara med segmenterat innehåll:

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

GET-begäran anger "Range"-huvudet till "bytes=0-1023", vilket är intervallet för byte. Om slutpunkten stöder begäranden om partiellt innehåll svarar slutpunkten med ett innehållssegment från det begärda intervallet. Baserat på slutpunkten kan det exakta formatet för rubrikfältet "Intervall" skilja sig åt.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Ladda upp innehåll i segment

Om du vill överföra segmenterat innehåll från en HTTP-åtgärd måste åtgärden `runtimeConfiguration` ha aktiverat stöd för segmentering via åtgärdens egenskap. Med den här inställningen kan åtgärden starta segmenteringsprotokollet. Logikappen kan sedan skicka ett första POST- eller PUT-meddelande till målslutpunkten. När slutpunkten har svarat med en föreslagen segmentstorlek följer logikappen upp genom att skicka HTTP PATCH-begäranden som innehåller innehållssegmenten.

De här stegen beskriver den detaljerade process som Logic Apps använder för att ladda upp segmenterat innehåll från logikappen till en slutpunkt:

1. Logikappen skickar en första HTTP POST- eller PUT-begäran med en tom meddelandetext. Målhuvudet innehåller den här informationen om innehållet som logikappen vill ladda upp i segment:

   | Sidfältet för begäran om logic apps-begäran | Värde | Typ | Beskrivning |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-läge** | Chunked | String | Anger att innehållet överförs i segment |
   | **x-ms-innehåll-längd** | <*innehållslängd*> | Integer | Hela innehållsstorleken i byte före segmentering |
   ||||

2. Slutpunkten svarar med statuskoden "200" och den här valfria informationen:

   | Sidhuvudfältet för slutpunktssvar | Typ | Krävs | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **x-ms-segment-storlek** | Integer | Inga | Den föreslagna segmentstorleken i byte |
   | **Location** | String | Ja | URL-platsen var HTTP PATCH-meddelandena ska skickas |
   ||||

3. Din logikapp skapar och skickar uppföljningsmeddelanden för HTTP PATCH – var och en med den här informationen:

   * Ett innehållssegment baserat på **x-ms-chunk-size** eller någon internt beräknad storlek tills allt innehåll som summerar **x-ms-content-längd** överförs sekventiellt upp

   * Dessa rubrikinformation om innehållssegmentet som skickas i varje PATCH-meddelande:

     | Sidfältet för begäran om logic apps-begäran | Värde | Typ | Beskrivning |
     |---------------------------------|-------|------|-------------|
     | **Innehållsområde** | <*Utbud*> | String | Byteintervallet för det aktuella innehållssegmentet, inklusive startvärdet, slutvärdet och den totala innehållsstorleken, till exempel: "bytes=0-1023/10100" |
     | **Innehållstyp** | <*innehållstyp*> | String | Typ av segmenterat innehåll |
     | **Innehållslängd** | <*innehållslängd*> | String | Storleken på storleken i byte för det aktuella segmentet |
     |||||

4. Efter varje PATCH-begäran bekräftar slutpunkten inleveransen för varje segment genom att svara med statuskoden "200" och följande svarsrubriker:

   | Sidhuvudfältet för slutpunktssvar | Typ | Krävs | Beskrivning |
   |--------------------------------|------|----------|-------------|
   | **Intervall** | String | Ja | Byteintervallet för innehåll som har tagits emot av slutpunkten, till exempel: "bytes=0-1023" |   
   | **x-ms-segment-storlek** | Integer | Inga | Den föreslagna segmentstorleken i byte |
   ||||

Den här åtgärdsdefinitionen visar till exempel en HTTP POST-begäran om överföring av segmenterat innehåll till en slutpunkt. I åtgärdens `runTimeConfiguration` egenskap anger `contentTransfer` `transferMode` egenskapen `chunked`till:

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
