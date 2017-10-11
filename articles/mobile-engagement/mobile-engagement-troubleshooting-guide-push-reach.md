---
title: "Azure Mobile Engagement Guide - Push/räckvidden för felsökning"
description: "Felsökning av problem med användarens interaktion och meddelanden i Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 3f1886b7-1fdd-47f4-b6b0-d79f158d5ef3
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ef6f34404b97a6972fc136262920a1bdbc4117b0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Felsökningsguide för problem med push och reach
Följande är möjliga problem som kan uppstå med hur Azure Mobile Engagement skickar information till användarna.

## <a name="push-failures"></a>Push-fel
### <a name="issue"></a>Problem
* Push-meddelanden fungerar inte (i appen, utanför appen eller båda).

### <a name="causes"></a>Orsaker
* Många gånger ett push fel är en uppgift att Azure Mobile Engagement, Reach eller andra avancerade funktioner för Azure Mobile Engagement inte är korrekt integrerad eller att en uppgradering krävs i SDK för att åtgärda ett känt problem med en ny plattform OS eller enhet.
* Testa bara ett push i appen och bara en av App push att avgöra om det är något problem i App eller Out-of-App.
* Testa från både Användargränssnittet och API: et som en felsökningsåtgärd för att se vilka ytterligare felinformation är tillgänglig båda platserna.
* Utanför appen fungerar inte push-meddelanden om räckvidd och Azure Mobile Engagement är integrerade i SDK.
* Push-meddelanden fungerar inte om certifikat inte är giltiga eller använder PROD vs. DEV korrekt (endast iOS). (**Obs:** ”utanför appen” push-meddelanden kan inte levereras till iOS, om du har både utveckling (utveckling) och produktion (PROD) versioner av programmet installeras på samma enhet eftersom säkerhetstoken som är associerade med din certifikat kan vara ogiltig från Apple. Lös problemet, avinstallera DEV och PROD versioner av programmet och installera om endast en version på enheten.)
* Utanför appen push antal hanteras annorlunda på olika plattformar (iOS visar mindre information än Android om native push-meddelanden är inaktiverade på en enhet, API: et kan ge mer information än Användargränssnittet på push stats).
* Utanför appen blockeras push-meddelanden av kunder på OS-nivå (iOS och Android).
* Utanför appen visas push-meddelanden som inaktiverad i Användargränssnittet för Azure Mobile Engagement om de inte är integrerad korrekt, men misslyckas tyst från API: et.
* Push-meddelanden i appen kommer inte att fungera om räckvidd och Azure Mobile Engagement är integrerade i SDK.
* GCM och ADM push-meddelanden fungerar inte om inte Azure Mobile Engagement och specifika servern är integrerade i SDK (endast Android).
* I appen och ut från appen bör push-meddelanden testas separat för att fastställa om det är ett Push- eller Reach-problem.
* I appen push-meddelanden kräver att appen öppnas som ska tas emot.
* Push-meddelanden är ofta installationen som ska filtreras med opt i eller gå ur appen info koden i appen.
* Om du använder en anpassad kategori i Reach meddelandevisning i appen, måste du följa rätt livscykeln för meddelandet, annars meddelandet kan inte tas bort när användaren stänger du den.
* Om du startar en kampanj med inget slutdatum och en enhet tar emot appen i meddelandet, men har inte visa det ännu, ska användaren fortfarande ta emot meddelandet nästa gång de loggar in på app även om du manuellt avslutas kampanjen.
* Bekräfta att du verkligen vill använda Push-API i stället för att nå-API (eftersom Reach-API används oftare) och att du inte förvirrande parametrarna ”nyttolast” och ”meddelaren” för problem med Push-API.
* Testa din push-kampanj med både en enhet är ansluten via Wi-Fi och 3G eliminerar nätverksanslutning som en möjlig problem.

## <a name="push-testing"></a>Push-testning
### <a name="issue"></a>Problem
* Push-meddelanden kan skickas till en specifik enhet baserat på en enhet-ID.

### <a name="causes"></a>Orsaker
* Testenheter ställs in på olika sätt för varje plattform, men orsakar en händelse i din app på en testenhet och letar efter enhets-ID i portalen ska fungera om du vill hitta enhets-ID för alla plattformar.
* Testenheter fungerar olika med IDFA vs. IDFV (endast iOS).

## <a name="push-customization"></a>Push-anpassning
### <a name="issue"></a>Problem
* Avancerade push innehåll objektet inte fungerar (badge, ring, vibrerar, bild, osv.).
* Länkar från push-meddelanden fungerar inte (i appen, utanför appen, till en webbplats till en plats i appen).
* Push-statistik visar att ett push inte skickades till så många personer som förväntat (för många eller finns inte tillräckligt med).
* Push dubbletter och mottagna två gånger.
* Det går inte att registrera testenhet för Azure Mobile Engagement push-meddelanden (med din egen produktprenumeration eller DEV app).

### <a name="causes"></a>Orsaker
* Om du vill länka till en specifik plats i appen kräver ”kategorier” (endast Android).
* Djupgående länkade scheman för att omdirigera användare till en annan plats när du klickar på ett push-meddelande måste skapas på och hanteras av ditt program och enhetens operativsystem inte av Mobile Engagement direkt. (**Obs:** utanför appen meddelanden kan inte länka direkt till i iOS app platser som de kan göra med Android.)
* Externa bilden servrar behöver för att kunna använda HTTP ”GET” och ”gå” för stor bild push-meddelanden ska fungera (endast Android).
* I din kod du inaktivera Azure Mobile Engagement-agenten när tangentbordet har öppnats och att koden aktivera Azure Mobile Engagement-agenten igen när tangentbordet har stängts så att tangentbordet inte påverkar hur ditt meddelande (endast iOS).
* Vissa objekt fungerar inte i test simulering, men endast verkliga kampanjer (badge, ring, vibrerar, bild, osv.).
* Inga data för server-sida loggas när du använder knappen till ”test” push-meddelanden. Data loggas endast för verkliga push-kampanjer.
* Felsöka med för att isolera problemet: testa, simulera, och en verklig kampanj eftersom de fungerar lite annorlunda.
* Hur lång tid dina ”i app” och ”helst” kampanjer är schemalagda att köras kan påverka överföringen siffror eftersom en kampanj levereras endast för användare ”i appen” när kampanjen körs (och användare som har sina Enhetsinställningar anger att ta emot meddelanden ”ut appen ”).
* Skillnader mellan hur Android och iOS hanteras från appen meddelanden gör det svårt att jämföra direkt push statistik mellan Android och iOS-versionen av programmet. Android ger OS nivån meddelande mer än iOS. Android rapporterar när ett enhetligt meddelande tas emot, klickar på eller tas bort i meddelandecentret, men iOS rapporterar inte den här informationen om inte användaren klickar på meddelandet. 
* Det främsta skälet som ”intryckt” siffror skiljer sig annorlunda än ”levererade” nummer för nå kampanjer är att ”i app” och ”utanför appen” meddelanden räknas inte på samma sätt. ”I app” meddelanden hanteras av Mobile Engagement, men ”utanför appen” meddelanden hanteras av meddelandecentret i OS av enheten.

## <a name="push-targeting"></a>Push-mål
### <a name="issue"></a>Problem
* Inbyggda riktad fungerar inte som förväntat.
* Appinfo taggen riktad fungerar inte som förväntat.
* Målobjekt för geografiska plats fungerar inte som förväntat.
* Språkinställningar fungerar inte som förväntat.

### <a name="causes"></a>Orsaker
* Kontrollera att du har överfört app-info taggar via Azure Mobile Engagement Användargränssnittet eller API.
* Begränsning push-hastigheten eller push kvot på programnivå eller begränsa målgruppen på nivån kampanj kan förhindra att en person som tar emot vissa push även om de uppfyller dina målobjekt kriterier. 
* Ange ”språk” skiljer sig riktad baserat på land eller språk, som också är annat än riktad baserat på geografiska plats på en GPS-plats eller phone plats.
* Meddelandet ”standardspråket” skickas till alla kunder som inte har sin enhet som har angetts till ett av de alternativa språk som du anger.

## <a name="push-scheduling"></a>Push-planering
### <a name="issue"></a>Problem
* Push schemaläggning fungerar inte som förväntat (har skickats för tidig eller fördröjd).

### <a name="causes"></a>Orsaker
* Tidszoner kan problem med schemaläggning, särskilt när du använder på slutanvändarens tidszon.
* Avancerade push-funktioner kan fördröja push-meddelanden.
* Målobjekt för baserat på telefon inställningar (i stället för taggar för App-Info) kan fördröja push-meddelanden eftersom Azure Mobile Engagement kan behöva begära data från phone realtid innan du skickar ett push.
* Kampanjer som skapats utan ett slutdatum lagra push lokalt på enheten och visar den nästa gång appen öppnas även om kampanjen manuellt avslutas.
* Starta mer än en kampanj samtidigt kan det ta längre tid att skanna användarbasen (testa att endast starta en kampanj samtidigt med högst fyra, även target bara aktiva användare så att gammal användarna inte behöver genomsökas).
* Om du använder alternativet ”Ignorera målgrupp, push skickas till användarna via API: et” i avsnittet ”kampanj” i en Reach-kampanj kampanjen skickas inte automatiskt, måste skicka manuellt via Reach-API.
* Om du använder en anpassad kategori i Reach meddelandevisning i appen, måste du följa rätt livscykeln för ett meddelande, annars meddelandet kan inte tas bort när användaren stänger du den.

