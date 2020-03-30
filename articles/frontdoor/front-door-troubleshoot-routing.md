---
title: Felsöka konfigurationsproblem för Front Door i Azure
description: I den här självstudien får du lära dig att felsöka några av de vanliga problem som du kan ställas inför för ytterdörren.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471514"
---
# <a name="troubleshooting-common-routing-issues"></a>Felsöka vanliga routningsproblem

I den här artikeln beskrivs hur du felsöker några av de vanliga routningsproblem som du kan ställas inför för konfigurationen av Front Door i Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 svar från ytterdörren efter några sekunder

### <a name="symptom"></a>Symptom

- Regelbundna förfrågningar som skickas till din serverdel utan att gå igenom ytterdörren lyckas, men att gå via Ytterdörren resulterar i 503 felsvar.

- Felet från ytterdörren visar efter några sekunder (vanligtvis runt efter 30 sekunder)

### <a name="cause"></a>Orsak

Det här symptomet uppstår när antingen serverdelen tar längre än timeout-konfigurationen (standard är 30 sekunder) för att ta emot begäran från ytterdörren eller om det tar mer än det här timeout-värdet för att skicka ett svar på begäran från ytterdörren. 

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

- Skicka begäran till din backend direkt (utan att gå igenom ytterdörren) och se vad som är den vanliga tiden det tar för din backend att svara.
- Skicka begäran via ytterdörren och se om du ser några 503 svar. Om inte, då detta kanske inte är en timeout fråga. Vänligen kontakta supporten.
- Om du går via ytterdörren resulterar i 503 felsvarskod konfigurerar du fältet sendReceiveTimeout för ytterdörren så att standardtidsgränsen utökas till 4 minuter (240 sekunder). Inställningen är under `backendPoolSettings` och `sendRecvTimeoutSeconds`anropas . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Begäranden som skickas till den anpassade domänen returnerar 400-statuskod

### <a name="symptom"></a>Symptom

- Du har skapat en ytterdörr, men en begäran till domänen eller klientdelsvärden returnerar en HTTP 400-statuskod.

- Du har skapat en DNS-mappning från en anpassad domän till den klientdelsvärd som du har konfigurerat. Om du skickar en begäran till det anpassade domännamnet returneras en HTTP 400-statuskod och den visas inte väg till de serverdelar som du har konfigurerat.

### <a name="cause"></a>Orsak

Det här problemet kan inträffa om du inte har konfigurerat en routningsregel för den anpassade domänen som du har lagt till som frontend-värd. En routningsregel måste uttryckligen läggas till för den klientdelsvärden, även om en redan har konfigurerats för frontend-värden under underdomänen för ytterdörren (*.azurefd.net) som din anpassade domän har en DNS-mappning till.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Lägg till en routningsregel från den anpassade domänen i önskad backend-pool.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Ytterdörren omdirigerar inte HTTP till HTTPS

### <a name="symptom"></a>Symptom

Ytterdörren har en routningsregel som säger omdirigera HTTP till HTTPS, men åtkomst till domänen behåller fortfarande HTTP som protokoll.

### <a name="cause"></a>Orsak

Detta kan inträffa om du inte har konfigurerat routningsreglerna för ytterdörren korrekt. I grund och botten är din nuvarande konfiguration inte specifik och kan ha motstridiga regler.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Begäran till frontend-värdnamn returnerar 404-statuskod

### <a name="symptom"></a>Symptom

- Du har skapat en ytterdörr och konfigurerat en klientdelsvärd, en serverdelspool med minst en serverdel i den och en routningsregel som ansluter frontend-värden till serverdelspoolen. Ditt innehåll verkar inte vara tillgängligt när du skickar en begäran till den konfigurerade klientdelsvärden eftersom en HTTP 404-statuskod returneras.

### <a name="cause"></a>Orsak

Det finns flera möjliga orsaker till detta symptom:

- Backend är inte en offentlig vänd bakifrån och är inte synlig för ytterdörren.
- Serverdelen är felkonfigurerad, vilket gör att ytterdörren skickar fel begäran (det vill säga din serverdel accepterar bara HTTP men du har inte avmarkerat tillåter HTTPS så ytterdörren försöker vidarebefordra HTTPS-begäranden).
- Den bakre sidan avvisar värdhuvudet som vidarebefordrades med begäran till backend.
- Konfigurationen för serveringen har ännu inte distribuerats helt.

### <a name="troubleshooting-steps"></a>Felsökningsanvisningar

1. Driftsättningstid
   - Se till att du har väntat ~ 10 minuter för konfigurationen som ska distribueras.

2. Kontrollera backend-inställningarna
    - Navigera till serverda poolen som begäran ska dirigeras till (beror på hur du har routningsregeln konfigurerad) och kontrollera att _serverdavärdens typ_ och servernamnsvärden är korrekta. Om backend är en anpassad värd, se till att du har stavat det korrekt. 

    - Kontrollera HTTP- och HTTPS-portarna. I de flesta fall, 80 och 443 (respektive), är korrekta och inga ändringar kommer att krävas. Det finns dock en chans att din serverd inte är konfigurerad på detta sätt och lyssnar på en annan port.

        - Kontrollera _serverdelshuvudet_ som konfigurerats för de serverdelsvärden som frontend-värden ska dirigera till. I de flesta fall bör det här huvudet vara samma som _värdnamnet för backend_. Ett felaktigt värde kan dock orsaka olika HTTP 4xx-statuskoder om backend förväntar sig något annat. Om du anger IP-adressen för din backend kan du behöva ange _värdhuvudet för backend_ till värdnamnet för backend.


3. Kontrollera inställningarna för routningsregel
    - Navigera till routningsregeln som ska dirigeras från frontend-värdnamnet i fråga till en backend-pool. Kontrollera att de godkända protokollen är korrekt konfigurerade, eller om inte, se till att protokollets ytterdörr används när vidarebefordran av begäran är korrekt konfigurerad. Fältet _godkända protokoll_ avgör vilka begäranden som ska accepteras och _vidarebefordringsprotokollet_ bestämmer vilket protokoll ytterdörren ska använda för att vidarebefordra begäran till klientdelen.
         - Om serveringsformuläret bara accepterar HTTP-begäranden är följande konfigurationer giltiga:
            - _Godkända protokoll_ är HTTP och HTTPS. _Vidarebefordringsprotokoll_ är HTTP. Matchbegäran fungerar inte, eftersom HTTPS är ett tillåtet protokoll och om en begäran kom in som HTTPS, skulle Ytterdörren försöka vidarebefordra den med HTTPS.

            - _Godkända protokoll_ är HTTP. _Vidarebefordringsprotokollet_ är antingen matchningsbegäran eller HTTPS.

    - _Url Skriv om_ är inaktiverad som standard och du bör bara använda det här fältet om du vill begränsa omfattningen av de resurser som du vill göra för att göra de använda backend-värdena som du vill göra tillgängliga. När den är inaktiverad vidarebefordrar ytterdörren samma sökväg som den tar emot. Det är möjligt att det här fältet är felkonfigurerat och ytterdörren begär en resurs från serverdelen som inte är tillgänglig, vilket returnerar en HTTP 404-statuskod.

