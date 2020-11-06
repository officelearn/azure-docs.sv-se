---
title: Felsöka konfigurations problem i Azure front dörr
description: I den här självstudien får du lära dig att själv felsöka några vanliga problem som du kan använda för din front dörr.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 8e810a31fab4457e47329e37f54b16e6f488c9da
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337635"
---
# <a name="troubleshooting-common-routing-issues"></a>Felsöka vanliga problem med Routning

I den här artikeln beskrivs hur du felsöker några vanliga synkroniseringsproblem som du kan använda för att konfigurera din Azure-frontend.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 svar från Front dörren efter några sekunder

### <a name="symptom"></a>Symptom

* Vanliga förfrågningar som skickas till din server del utan att gå igenom front dörren lyckas, men att gå via Front dörren leder till 503 fel svar.
* Felen från Front dörren visas efter några sekunder (vanligt vis omkring efter 30 sekunder)

### <a name="cause"></a>Orsak

Orsaken till det här problemet kan vara något av två saker:
 
* Server delen tar längre tid än den konfigurerade tids gränsen (Standardvärdet är 30 sekunder) för att ta emot begäran från Front dörren.
* Hur lång tid det tar att skicka ett svar till begäran från Front dörren tar längre tid än tids gräns värdet. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

* Skicka begäran till Server delen direkt (utan att gå igenom front dörren) och se vad som är den normala tid det tar för din server del att svara.
* Skicka begäran via Front dörren och se om du får 503 svar. Om inte, kanske problemet inte är ett timeout-problem. Kontakta supporten.
* Om du går igenom den främre dörren ger du 503 fel svars kod och konfigurerar sedan `sendReceiveTimeout` fältet för din front dörr. Du kan utöka standard tids gränsen upp till 4 minuter (240 sekunder). Inställningen är under `backendPoolSettings` och kallas `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Begär Anden som skickas till den anpassade domänen returnerar 400 status kod

### <a name="symptom"></a>Symptom

* Du har skapat en frontend-dörr men en begäran till domänen eller klient dels värden returnerar en HTTP 400-status kod.
* Du har skapat en DNS-mappning för en anpassad domän till den klient dels värd som du konfigurerade. Men om du skickar en begäran till det anpassade domän namnet, returneras en status kod för HTTP 400. Som inte visas för att dirigera till den server del som du har konfigurerat.

### <a name="cause"></a>Orsak

Problemet uppstår om du inte har konfigurerat en regel för routning för den anpassade domänen som lades till som klient dels värd. En regel för routning måste läggas till explicit för den klient dels värden. Även om en sådan redan har kon figurer ATS för klient dels värden under under domänen front dörr (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lägg till en routningsprincip för den anpassade domänen för att dirigera trafik till den valda backend-poolen.

## <a name="front-door-doesnt-redirect-http-to-https"></a>Front dörren omdirigerar inte HTTP till HTTPS

### <a name="symptom"></a>Symptom

Din front dörr har en regel för routning som anger omdirigering av HTTP till HTTPS, men åtkomst till domänen upprätthåller HTTP som protokoll.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte konfigurerade routningsregler korrekt för din frontend-dörr. Den aktuella konfigurationen är i princip inte särskilt speciell och kan ha motstridiga regler.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Begäran till klient delens värdnamn returnerar 404 status kod

### <a name="symptom"></a>Symptom

 Du har skapat en frontend-dörr genom att konfigurera en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll är inte tillgängligt när du gör en begäran till den konfigurerade klient dels värden som ett resultat av att en status kod för HTTP 404 returneras.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet:

* Server delen är inte en offentlig Server del och är inte synlig för front dörren.
* Server delen är felkonfigurerad och gör att front dörren kan skicka fel förfrågan. Med andra ord accepterar din server del bara HTTP och du har inte avmarkerat tillåta HTTPS. Så front dörren försöker vidarebefordra HTTPS-begäranden.
* Server delen avvisar värd rubriken som vidarebefordrades med begäran till Server delen.
* Konfigurationen för Server delen har ännu inte distribuerats fullständigt.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

1. Distributions tid
   * Se till att du har väntat ~ 10 minuter för att konfigurationen ska distribueras.

2. Kontrol lera Server dels inställningarna
    * Navigera till den backend-pool som begäran ska dirigeras till (beror på hur du har konfigurerat Routningsprincipen). Verifiera att värd *typen för Server delen* och värd namnet för Server delen är korrekt. Om Server delen är en anpassad värd kontrollerar du att du har stavat den korrekt. 

    * Kontrol lera dina HTTP-och HTTPS-portar. I de flesta fall är 80 och 443 (respektive) korrekta och inga ändringar krävs. Det finns dock en chans att din server del inte har kon figurer ATS på det här sättet och lyssnar på en annan port.

        * Kontrol lera _Server dels värd rubriken_ som kon figurer ATS för de Server delar som klient dels värden ska dirigeras till. I de flesta fall ska den här rubriken vara samma som *Server dels värd namnet*. Ett felaktigt värde kan dock orsaka olika status koder för HTTP-4xx om Server delen förväntar sig något annat. Om du anger en IP-adress för Server delen kan du behöva ange *värd rubriken för Server delen* till Server delens värdnamn.

3. Kontrol lera inställningarna för routningsregler:
    * Navigera till regeln för routning som ska vidarebefordra från klient delens värdnamn i fråga till en backend-pool. Kontrol lera att de godkända protokollen är korrekt konfigurerade när du vidarebefordrar begäran. Fältet *godtagna protokoll* avgör vilka begär Anden som front dörren ska godkänna. *Vidarebefordrings protokollet* avgör vilken protokoll-front dörr som ska användas för att vidarebefordra begäran till Server delen.
         * Exempel: om Server delen bara accepterar HTTP-förfrågningar är följande konfigurationer giltiga:
            * *Godkända protokoll* är http och https. *Protokoll för vidarebefordran* är http. Matchnings förfrågan fungerar inte, eftersom HTTPS är ett tillåtet protokoll och om en begäran kom in som HTTPS, försöker front dörren vidarebefordra den med hjälp av HTTPS.

            * *Godkända protokoll* är http. *Forwarding-protokollet* matchar antingen Request eller http.
    - *URL-omskrivning* är inaktive rad som standard. Det här fältet används bara om du vill begränsa omfattningen av de Server dels värd resurser som du vill göra tillgängliga. När den är inaktive rad vidarebefordrar front dörren samma sökväg för begäran som den tar emot. Det är möjligt att felaktigt konfigurera det här fältet. Så när front dörren begär en resurs från Server delen som inte är tillgänglig, returneras en HTTP 404-status kod.

## <a name="request-to-frontend-host-name-returns-411-status-code"></a>Begäran till klient dels värd namnet returnerar 411 status kod

### <a name="symptom"></a>Symptom

Du har skapat en frontend-dörr och konfigurerat en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll verkar inte vara tillgängligt när du skickar en begäran till den konfigurerade klient dels värden eftersom en status kod för HTTP 411 returneras.

Svar på dessa förfrågningar kan också innehålla en HTML-felsida i svars texten som innehåller en för klar ande instruktion. Exempelvis: `HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet. den övergripande orsaken är dock att din HTTP-begäran inte är helt RFC-kompatibel. 

Ett exempel på inkompatibilitet är en `POST` begäran som skickas utan något `Content-Length` eller en `Transfer-Encoding` rubrik (till exempel med `curl -X POST https://example-front-door.domain.com` ). Den här begäran uppfyller inte kraven som anges i [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) och blockeras av din front dörr med ett HTTP 411-svar.

Det här beteendet är skilt från WAF-funktionerna i front dörren. För närvarande finns det inget sätt att inaktivera det här beteendet. Alla HTTP-begäranden måste uppfylla kraven, även om WAF-funktionen inte används.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

- Kontrol lera att dina begär Anden uppfyller de krav som anges i de nödvändiga rapporterna.

- Anteckna vilken HTML-meddelande text som returneras som svar på din begäran, eftersom de ofta förklarar exakt *hur* din begäran är icke-kompatibel.
