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
ms.date: 09/22/2018
ms.author: duau
ms.openlocfilehash: babe24d0c934cffac00a5100d1da7ee252d147da
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399063"
---
# <a name="troubleshooting-common-routing-issues"></a>Felsöka vanliga problem med Routning

I den här artikeln beskrivs hur du felsöker några vanliga synkroniseringsproblem som du kan använda för att konfigurera din Azure-frontend.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 svar från Front dörren efter några sekunder

### <a name="symptom"></a>Symptom

- Vanliga förfrågningar som skickas till din server del utan att gå igenom front dörren lyckas, men att gå via Front dörren leder till 503 fel svar.

- Felen från Front dörren visas efter några sekunder (vanligt vis omkring efter 30 sekunder)

### <a name="cause"></a>Orsak

Det här problemet uppstår när antingen din server del tar längre tid än timeout-konfigurationen (Standardvärdet är 30 sekunder) för att ta emot begäran från Front dörren eller om det tar längre tid att skicka ett svar till begäran från Front dörren. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

- Skicka begäran till Server delen direkt (utan att gå igenom front dörren) och se vad som är den normala tid det tar för din server del att svara.
- Skicka begäran via Front dörren och se om du ser några 503 svar. Om inte, kan det bero på att detta inte är ett tids gräns problem. Kontakta supporten.
- Om du fortsätter via frontend-dörren till 503-felsvars koden konfigurerar du fältet sendReceiveTimeout för din front dörr för att utöka standard tids gränsen upp till 4 minuter (240 sekunder). Inställningen är under `backendPoolSettings` och kallas `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Begär Anden som skickas till den anpassade domänen returnerar 400 status kod

### <a name="symptom"></a>Symptom

- Du har skapat en frontend-dörr men en begäran till domänen eller klient dels värden returnerar en HTTP 400-status kod.

- Du har skapat en DNS-mappning från en anpassad domän till den klient dels värd som du har konfigurerat. Att skicka en begäran till den anpassade domänens värdnamn returnerar dock en HTTP 400-statuskod och visas inte för att dirigera till den eller de Server delar som du har konfigurerat.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte har konfigurerat en regel för routning för den anpassade domänen som du har lagt till som en klient dels värd. En regel för routning måste uttryckligen läggas till för den klient dels värden, även om en sådan redan har kon figurer ATS för klient dels värden under under domänen för den främre dörren (*. azurefd.net) som din anpassade domän har en DNS-mappning till.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lägg till en regel för routning från den anpassade domänen till önskad backend-pool.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Front dörren omdirigerar inte HTTP till HTTPS

### <a name="symptom"></a>Symptom

Din front dörr har en regel för routning som anger omdirigering av HTTP till HTTPS, men åtkomst till domänen upprätthåller HTTP som protokoll.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte har konfigurerat routningsregler för din front dörr korrekt. Den aktuella konfigurationen är i princip inte särskilt speciell och kan ha motstridiga regler.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Begäran till klient delens värdnamn returnerar 404 status kod

### <a name="symptom"></a>Symptom

- Du har skapat en frontend-dörr och konfigurerat en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll verkar inte vara tillgängligt när du skickar en begäran till den konfigurerade klient dels värden eftersom en status kod för HTTP 404 returneras.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till det här problemet:

- Server delen är inte en offentlig Server del och är inte synlig för front dörren.
- Server delen är felkonfigurerad, vilket gör att front dörren kan skicka fel begäran (det vill säga att Server delen bara accepterar HTTP, men du har inte avmarkerat tillåta HTTPS, så att en front dörr försöker vidarebefordra HTTPS-begäranden).
- Server delen avvisar värd rubriken som vidarebefordrades med begäran till Server delen.
- Konfigurationen för Server delen har ännu inte distribuerats fullständigt.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

1. Distributions tid
   - Se till att du har väntat ~ 10 minuter för att konfigurationen ska distribueras.

2. Kontrol lera Server dels inställningarna
    - Navigera till den backend-pool som begäran ska dirigeras till (beroende på hur du har konfigurerat Routningsprincipen) och kontrol lera att värd _typen för Server delen_ och Server delens värd namn är korrekta. Om Server delen är en anpassad värd kontrollerar du att du har stavat den korrekt. 

    - Kontrol lera dina HTTP-och HTTPS-portar. I de flesta fall är 80 och 443 (respektive) korrekta och inga ändringar krävs. Det finns dock en chans att din server del inte har kon figurer ATS på det här sättet och lyssnar på en annan port.

        - Kontrol lera _Server dels värd rubriken_ som kon figurer ATS för de Server delar som klient dels värden ska dirigeras till. I de flesta fall ska den här rubriken vara samma som _Server dels värd namnet_. Ett felaktigt värde kan dock orsaka olika status koder för HTTP-4xx om Server delen förväntar sig något annat. Om du anger en IP-adress för Server delen kan du behöva ange _värd rubriken för Server delen_ till Server delens värdnamn.


3. Kontrol lera inställningarna för routningsregler
    - Navigera till regeln för routning som ska vidarebefordra från klient delens värdnamn i fråga till en backend-pool. Kontrol lera att de godkända protokollen är korrekt konfigurerade, eller om inte, se till att protokollets front dörr kommer att användas när begäran vidarebefordras. Fältet _godtagna protokoll_ avgör vilka begär Ande som front dörren ska godkänna och _vidarebefordrings protokollet_ avgör vilken protokoll klient dörren ska använda för att vidarebefordra begäran till Server delen.
         - Exempel: om Server delen bara accepterar HTTP-förfrågningar är följande konfigurationer giltiga:
            - _Godkända protokoll_ är http och https. _Protokoll för vidarebefordran_ är http. Matchnings förfrågan fungerar inte, eftersom HTTPS är ett tillåtet protokoll och om en begäran kom in som HTTPS, försöker front dörren vidarebefordra den med hjälp av HTTPS.

            - _Godkända protokoll_ är http. _Forwarding-protokollet_ matchar antingen Request eller http.

    - _URL-omskrivning_ är inaktive rad som standard och du bör endast använda det här fältet om du vill begränsa omfattningen av Server dels värd resurser som du vill göra tillgängliga. När den är inaktive rad vidarebefordrar front dörren samma sökväg för begäran som den tar emot. Det är möjligt att det här fältet är felkonfigurerat och att front dörren begär en resurs från Server delen som inte är tillgänglig, vilket returnerar en status kod för HTTP 404.

