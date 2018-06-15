---
title: Säkerhet för din StorSimple-enhet | Microsoft Docs
description: Beskriver konventioner för säkerhet, riktlinjer och överväganden och förklarar hur du på ett säkert sätt installera och använda din StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: e45b09a62f33fd9811714febd63a74149784980d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875104"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Installera och använda din StorSimple-enhet på ett säkert sätt
![Varningsikon](./media/storsimple-safety/IC740879.png)
![läsa säkerhet meddelande ikonen](./media/storsimple-safety/IC740885.png) **läsa hälsa och INFORMATION**

Läsa alla hälsa och informationen i den här artikeln gäller för din Microsoft Azure StorSimple-enhet. Behåll alla utskrivna stödlinjer som medföljer din StorSimple-enhet för framtida bruk. Underlåtenhet att följa anvisningarna och korrekt konfigurera, använda och hand för den här produkten kan öka risken för allvarlig skada eller död eller skada på enheterna. En [nedladdningsbar version av den här guiden](http://www.microsoft.com/download/details.aspx?id=44233) är också tillgänglig.

## <a name="safety-icon-conventions"></a>Ikonen konventioner för säkerhet
Här följer de ikoner som du hittar när du granska vilka säkerhetsåtgärder som beaktas när du konfigurerar och kör din Microsoft Azure StorSimple-enhet.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Ikon för risk](./media/storsimple-safety/IC740879.png) **risk!** |Visar en farliga situation som, om inte undvikas resulterar i död eller allvarliga skador. Signal ordet ska begränsas till de mest extrema fall. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!** |Visar en farliga situation som, om inte undvikas, kan leda till död eller allvarliga skador. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!** |Visar en farliga situation som, om inte undvikas, kan leda till lägre eller måttlig skada. |
| ![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:** |Anger information som anses vara viktiga, men inte risker relaterade. |
| ![Ikon för elektrisk undanröjs](./media/storsimple-safety/IC740882.png) **elektrisk undanröjs risk** |Hög spänning |
| ![Ikon för tunga vikt](./media/storsimple-safety/IC740883.png) **tunga vikt** | |
| ![Ingen användbar användare delar ikonen](./media/storsimple-safety/IC740879.png) **inga användare användbar delar** |Inte kommer åt såvida inte korrekt utbildade. |
| ![Läsa säkerhet meddelande ikonen](./media/storsimple-safety/IC740885.png)**först läsa alla instruktioner** | |
| ![Risken tipsikon](./media/storsimple-safety/IC740886.png) **tips risk** | |

## <a name="handling-precautions"></a>Åtgärder för hantering
![Varningsikon](./media/storsimple-safety/IC740879.png) ![tunga vikt ikonen](./media/storsimple-safety/IC740883.png) **varning!** 

Att minska risken för skador:

* Ett helt konfigurerade hölje kan väga upp till 32 kg (70 pund); Försök inte att lyfta själv.
* Innan du flyttar höljet Se alltid till att det finns två personer att hantera vikt. Tänk på att en person som försöker lyfter denna vikt kan klara skador.
* Häva inte höljet av referenser i ström och kylning moduler (PCMs) finns på baksidan av enheten. Dessa är inte avsedda att vidta vikt.

## <a name="connection-precautions"></a>Åtgärder för anslutningen
![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektrisk undanröjs ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska risken för skador, elektrisk undanröjs eller död:

* Koppla från alla ange power för fullständig isolering när drivs av flera AC-källor.
* Koppla permanent från enheten innan du flyttar den eller om du tror att den är skadad på något sätt.
* Tillhandahålla en säker elektriska earth-anslutning för att ange strömkablar. Kontrollera att den erfarenhet av att arbeta på höljet uppfyller nationella och lokala innan du tillämpar power.
* Kontrollera att power anslutningen kopplas alltid före borttagningen av en PCM från höljet.
* Med hänsyn till att ansluta på Ange strömsladd huvudfönstret koppla från enheten, kontrollera att uttag finns nära utrustningen och är lätt tillgängliga.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektrisk undanröjs ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska sannolikheten för överhettning eller brand från elektriska anslutningar:

* Ange en lämplig kraftkälla med elektriska överlagring skydd att uppfylla de krav som anges i den tekniska specifikationen.
* Använd inte bifurcated strömkablar (leads ”Y”).
* För att uppfylla tillämpliga säkerhet, utsläpp och termiska krav inga försättsblad ska tas bort och alla fack måste fyllas i med plugin-program eller enhet tomma värden.
* Kontrollera att utrustningen som används på ett sätt som anges av tillverkaren. Om den här utrustningen används på ett sätt som angetts av tillverkaren, skrivas skyddet som utrustningen.

![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För att fungera korrekt i utrustningen och för att förhindra produkten:

* Det finns RJ45-portar på baksidan av enheten för en Ethernet-anslutning. Dessa måste inte vara ansluten till ett nätverk för telekommunikation.
* Se till att installera enheten i ett rack som kan hantera en fram till tillbaka kylning design.
* Alla plugin-program och tomt nivåer är en del av systemomslutningen. Dessa måste bara tas bort när en ersättning kan läggas till direkt. Systemet måste inte köras utan alla moduler eller tomma värden på plats.

## <a name="rack-system-precautions"></a>Rack system försiktighetsåtgärder
Följande säkerhetskrav måste beaktas när du monterar enheten i ett rack cab.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![risken tipsikon](./media/storsimple-safety/IC740886.png) **varning!**

Att minska risken för skador av ett tips över:

* Rack designen ska ha stöd för den totala vikten av installerade höljen och bör omfatta stabiliserande funktioner som är lämpliga för att förhindra att racket vältning eller som flyttas över under installationen eller normal användning.
* När du läser in ett rack, fylls rack nedifrån och tom från början av.
* Skjut inte mer än en höljet utanför racket åt gången för att undvika risken för racket toppling.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektrisk undanröjs ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska risken för skador, elektrisk undanröjs eller död:

* Racket ska ha en säker elektriska distributionssystem. Det måste ange över aktuella skydd för höljet och måste inte överlagras av det totala antalet höljen installerad. Elektrisk ström för förbrukning klassificering som visas på märkskylten bör beaktas.
* Elektriska distributionssystem måste ange en tillförlitlig grunden för varje enhet i racket.
* Designen av elektriska distributionssystem måste ta hänsyn till det totala grunden läckaget aktuella från alla strömkällor i alla höljen. Observera att varje strömförsörjning i varje hölje grunden läckage ström 1.0 mA högst 60 Hz 264 v. Racket kan kräva namngivning med ”hög LÄCKAGE aktuella. Grunden (earth) anslutning krävs innan du ansluter en tillgång ”.
* Rack, konfigurerades med höljen, måste uppfylla säkerhetskraven i UL 60950-1 och IEC 60950-1/EN 60950-1.

![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För korrekt kylning rack systemet:

* Se till att rack design tar hänsyn till maximalt höljet operativsystem temperatur av 35 grader (95 grader Fahrenheit).
* Systemet drivs med low-pressure bakre avgaser installation (ligger skapas av rack dörrar och hinder högst 5 Pascal [0,5 mätare]).

## <a name="power-cooling-module-pcm-precautions"></a>Power kylning modul (PCM) försiktighetsåtgärder
Enheten är avsedd att fungera med två PCMs. Var och en av PCMs har en strömförsörjning och fläktar med dubbla axel. Under ett kritiskt tillstånd tillåter systemet till ett fel på en strömförsörjning medan normal drift. Två PCMs (och därför strömförsörjning) måste alltid vara installerad. Redundant strömförsörjning tillhandahåller inte en enda PCM. Därför kan fel på med ett enda PCM medföra driftavbrott eller förlust av data.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektrisk undanröjs ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska risken för skador, elektrisk undanröjs eller död:

* Ta inte bort försättsbladen från PCM. Det finns en risk för elektrisk undanröjs i. Returnera PCM och få en ny [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).

![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För att fungera korrekt i utrustningen och för att förhindra produkten:

* Du måste ersätta misslyckade PCM inom 24 timmar. När en PCM har tagits bort för ersättning, måste ersätter slutföras inom 10 minuter efter borttagningen.
* Ta inte bort en PCM såvida inte en ersättning kan installeras direkt. Höljet köras inte utan alla moduler på plats.

## <a name="electrostatic-discharge-esd-precautions"></a>Åtgärder för elektrostatisk avslutning ESD)
![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

Se följande ESD-relaterade åtgärder.

* Se till att du har installerat och kontrolleras en lämplig antistatiska handleden eller Ankelstövlar bandet.
* Se alla vanliga ESD försiktighetsåtgärder när du arbetar med moduler och komponenter.
* Undvik kontakt med bakplan komponenter och modulen kopplingar.
* ESD skada omfattas inte av garanti.

## <a name="battery-disposal-precautions"></a>Batteri avyttring försiktighetsåtgärder
Strömförsörjningen använder en särskild batteri för att skydda innehållet i minne under tillfälligt, kortsiktig strömavbrott. Batteriet sitter i PCM. Tänk på följande information om batteriet.

![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!**

Att minska risken för shorts, brand, explosion, skador eller död:

* Avyttra används batterierna i enlighet med nationell/regional förordningar.
* Ta isär koden, krossa, eller värma ovan 60 grader (140 grader Fahrenheit) eller inte förbränna. Ersätt PCM batteri med ett angivet batteri. Användning av en annan batteri kan utgöra en risk för brand eller explosion.
* Använd skyddande linjeslut på batterierna om dessa tas bort från strömförsörjningen.

![Lägg märke till ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

När leverans eller annars transport batterierna av luft följer IATA Lithium batteri vägledningen finns på [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

När du har granskat dessa meddelanden för säkerhet, är nästa steg att packa upp, rack och kabel enheten.

## <a name="next-steps"></a>Nästa steg
* För 8100-enhet, gå till [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md).
* För 8600-enhet, gå till [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).

