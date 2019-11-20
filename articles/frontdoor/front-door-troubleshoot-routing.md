---
title: Felsöka konfigurations problem i Azure frontend-tjänsten
description: I den här självstudien får du lära dig att själv felsöka några vanliga problem som du kan använda för din front dörr.
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
ms.openlocfilehash: c0d6303620b92368e422b54beab4f9c346d022a5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184562"
---
# <a name="troubleshooting-common-routing-issues"></a>Felsöka vanliga problem med Routning
I den här artikeln beskrivs hur du felsöker några av de vanliga problemen i routningen som du kan använda för Azures konfiguration av frontend-tjänsten. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Värdnamn dirigerar inte till Server del och returnerar 400 status kod


### <a name="symptom"></a>Symptom
- Du har skapat en frontend-dörr, men en begäran till klient dels klienten returnerar en HTTP 400-status kod.

  - Du har skapat en DNS-mappning från en anpassad domän till den klient dels värd som du har konfigurerat. Att skicka en begäran till den anpassade domänens värdnamn returnerar dock en HTTP 400-statuskod och visas inte för att dirigera till den eller de Server delar som du har konfigurerat.

### <a name="cause"></a>Orsak
- Det här problemet kan inträffa om du inte har konfigurerat en regel för routning för den anpassade domänen som du har lagt till som en klient dels värd. En regel för routning måste uttryckligen läggas till för den klient dels värden, även om en sådan redan har kon figurer ATS för klient dels värden under under domänen för den främre dörren (*. azurefd.net) som din anpassade domän har en DNS-mappning till.

### <a name="troubleshooting-steps"></a>Fel söknings steg
- Lägg till en regel för routning från den anpassade domänen till önskad backend-pool.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Begäran till klient delens värdnamn returnerar 404 status kod

### <a name="symptom"></a>Symptom
- Du har skapat en frontend-dörr och konfigurerat en klient dels värd, en backend-pool med minst en server del i den och en routningsprincip som ansluter klient dels värden till backend-poolen. Ditt innehåll verkar inte vara tillgängligt när du skickar en begäran till den konfigurerade klient dels värden eftersom en status kod för HTTP 404 returneras.

### <a name="cause"></a>Orsak
Det finns flera möjliga orsaker till det här problemet:
 - Server delen är inte en offentlig Server del och är inte synlig för frontend-tjänsten.

- Server delen är felkonfigurerad, vilket gör att frontend-tjänsten kan skicka fel begäran (det vill säga att din server del bara accepterar HTTP, men du har inte avmarkerat tillåta HTTPS, så att front dörren försöker vidarebefordra HTTPS-begäranden).
- Server delen avvisar värd rubriken som vidarebefordrades med begäran till Server delen.
- Konfigurationen för Server delen har ännu inte distribuerats fullständigt.

### <a name="troubleshooting-steps"></a>Fel söknings steg
1. Distributions tid
    - Se till att du har väntat ~ 10 minuter för att konfigurationen ska distribueras.

2. Kontrol lera Server dels inställningarna
   - Navigera till den backend-pool som begäran ska dirigeras till (beroende på hur du har konfigurerat Routningsprincipen) och kontrol lera att värd _typen för Server delen_ och Server delens värd namn är korrekta. Om Server delen är en anpassad värd kontrollerar du att du har stavat den korrekt. 

   - Kontrol lera dina HTTP-och HTTPS-portar. I de flesta fall är 80 och 443 (respektive) korrekta och inga ändringar krävs. Det finns dock en chans att din server del inte har kon figurer ATS på det här sättet och lyssnar på en annan port.

     - Kontrol lera _Server dels värd rubriken_ som kon figurer ATS för de Server delar som klient dels värden ska dirigeras till. I de flesta fall ska den här rubriken vara samma som _Server dels värd namnet_. Ett felaktigt värde kan dock orsaka olika status koder för HTTP-4xx om Server delen förväntar sig något annat. Om du anger en IP-adress för Server delen kan du behöva ange _värd rubriken för Server delen_ till Server delens värdnamn.


3. Kontrol lera inställningarna för routningsregler
     - Navigera till regeln för routning som ska vidarebefordra från klient delens värdnamn i fråga till en backend-pool. Kontrol lera att de godkända protokollen är korrekt konfigurerade, eller om inte, se till att protokollets front dörr kommer att användas när begäran vidarebefordras. De _godkända protokollen_ avgör vilka begär Anden som måste godkännas och _protokollet för vidarebefordran_ avgör vilken protokoll klient dörren ska använda för att vidarebefordra begäran till Server delen.
          - Exempel: om Server delen bara accepterar HTTP-förfrågningar är följande konfigurationer giltiga:
               - _Godkända protokoll_ är http och https. _Protokoll för vidarebefordran_ är http. Matchnings förfrågan fungerar inte, eftersom HTTPS är ett tillåtet protokoll och om en begäran kom in som HTTPS, försöker front dörren vidarebefordra den med hjälp av HTTPS.

               - _Godkända protokoll_ är http. _Vidarebefordrande protokoll_ matchar antingen Request eller https.

   - _URL-omskrivning_ är inaktive rad som standard och du bör endast använda det här fältet om du vill begränsa omfattningen av Server dels värd resurser som du vill göra tillgängliga. När den är inaktive rad vidarebefordrar front dörren samma sökväg för begäran som den tar emot. Det är möjligt att det här fältet är felkonfigurerat och att front dörren begär en resurs från Server delen som inte är tillgänglig, vilket returnerar en status kod för HTTP 404.

