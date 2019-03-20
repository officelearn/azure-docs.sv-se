---
title: Felsökning – felsökning av problem med din Azure ytterdörren Service-konfiguration | Microsoft Docs
description: I den här självstudien får lära du att felsöka några vanliga problem som du kan stöta på för ytterdörren lokal.
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
ms.openlocfilehash: 7a261d65a7bd3eea150dd764c65b94ddd47466b3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100317"
---
# <a name="troubleshooting-common-routing-issues"></a>Felsöka vanliga problem för Routning
Den här artikeln beskriver hur du felsöker vanliga routning problem du kan stöta på för din Azure ytterdörren Service-konfiguration. 

## <a name="hostname-not-routing-to-backend-and-returns-400-status-code"></a>Värdnamnet inte routning till serverdelen och returnerar 400-statuskod


### <a name="symptom"></a>Symtom
- Du har skapat en ytterdörren men en begäran till Frontend-värden returneras statuskoden HTTP 400.

  - Du har skapat en DNS-mappning från en anpassad domän till frontend-värden som du har konfigurerat. Dock skicka en begäran om att det anpassade domänvärdnamnet returnerar statuskoden HTTP 400 och visas inte för dirigering till backend(s) som du har konfigurerat.

### <a name="cause"></a>Orsak
- Det här problemet kan inträffa om du inte har konfigurerat en routningsregel för den anpassade domänen som du har lagt till som en frontend-värd. En regel för vidarebefordran måste läggas uttryckligen för klientdel värden, även om någon har redan konfigurerats för frontend-värden under underdomänen ytterdörren (*. azurefd.net) att din anpassade domän har en DNS-mappning till.

### <a name="troubleshooting-steps"></a>Felsökningssteg
- Lägg till en regel för vidarebefordran från den anpassade domänen till önskad serverdelspoolen.

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Begäran till Frontend-värdnamn returnerar 404-statuskod

### <a name="symptom"></a>Symtom
- Du har skapat en ytterdörren och konfigurerat en frontend-värd, en serverdelspool med minst en serverdel i den och en routningsregel som ansluter frontend-värden till i serverdelspoolen. Ditt innehåll verkar inte vara tillgängliga när du skickar en begäran till den Konfigurera frontend-värden eftersom en HTTP 404-statuskod returneras.

### <a name="cause"></a>Orsak
Det finns flera tänkbara orsaker till problemet:
 - Serverdelen är inte en offentlig riktade serverdel och visas inte för tjänsten ytterdörren.

- Serverdelen är felaktigt konfigurerad som orsakar ytterdörren-tjänsten för att skicka fel begäran (det vill säga serverdelen accepterar endast HTTP, men du inte har alternativet är avmarkerat så att HTTPS så ytterdörren försöker vidarebefordra HTTPS-begäranden).
- Serverdelen avvisar du värdhuvudet som vidarebefordrades med begäran till serverdelen.
- Konfigurationen för serverdelen har inte distribuerats helt ännu.

### <a name="troubleshooting-steps"></a>Felsökningssteg
1. Tidpunkten för distribution
    - Se till att du har väntat ~ 10 minuter för att konfigurationen ska distribueras.

2. Kontrollera inställningarna för serverdel
   - Gå till serverdelspoolen som begäran bör routning till (beror på hur du har en routningsregel för konfigurerats) och kontrollera att den _serverdel värdtyp_ och backend-värdnamnet är korrekta. Om serverdelen är en anpassad värd kan du se till att du har stavat rätt. 

   - Kontrollera din HTTP och HTTPS-portar. I de flesta fall 80 och 443 (respektive), är korrekta och inga ändringar kommer att krävas. Det finns dock en risk att serverdelen inte är konfigurerad på så sätt och lyssnar på en annan port.

     - Kontrollera den _serverdel värdhuvud_ konfigurerats för servrar som ska routning Frontend-värden till. I de flesta fall bör den här rubriken vara samma som den _serverdel värdnamn_. Ett felaktigt värde kan dock orsaka olika HTTP 4xx-statuskoder om serverdelen förväntar sig något annat. Om du anger IP-adressen för din serverdel kan du behöva ange den _serverdel värdhuvud_ till värdnamnet för serverdelen.


3. Kontrollera Regelinställningarna för Routning
     - Navigera till en routningsregel cirkulera från Frontend-värdnamnet i fråga till en serverdelspool. Kontrollera att de godkända protokoll är korrekt konfigurerade, eller om inte, kontrollera att protokollet ytterdörren kommer att använda när du vidarebefordrar begäran är korrekt konfigurerad. Den _godkänt protokoll_ avgör åtkomsten ska acceptera begäran och _vidarebefordran protokollet_ under den _Avancerat_ fliken avgör vilket protokoll ytterdörren ska använda för att vidarebefordra begäran till serverdelen.
          - Till exempel om serverdelen accepterar endast HTTP-begäranden är följande konfigurationer giltiga:
               - _Godkänt protokoll_ är HTTP och HTTPS. _Vidarebefordran av protokollet_ är HTTP. Matcha begäran fungerar inte eftersom HTTPS är ett tillåtna protokoll och om en begäran skickades som HTTPS, ytterdörren försöker att vidarebefordra den med hjälp av HTTPS.

               - _Godkänt protokoll_ är HTTP. _Vidarebefordran av protokollet_ är antingen matcha begäran- eller HTTPS.

   - Klicka på den _Avancerat_ fliken högst upp i fönstret för konfiguration av Routning regeln. _URL-Omskrivningsregler_ är inaktiverad som standard och du bör endast använda det här fältet om du vill begränsa omfånget för backend-värdbaserade resurser som du vill göra tillgängliga. När inaktiverad, vidarebefordrar ytterdörren samma sökväg för begäran som den tar emot. Det är möjligt att det här fältet är felaktigt konfigurerad och ytterdörren begär en resurs från serverdelen som inte är tillgänglig, därför returnera ett HTTP 404-statuskod.

