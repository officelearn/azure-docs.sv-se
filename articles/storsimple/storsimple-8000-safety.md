---
title: Säkerhet för StorSimple-enheten | Microsoft Docs
description: Beskrivs konventioner för säkerhet, riktlinjer och överväganden och förklarar hur du installerar och använder din StorSimple-enhet på ett säkert sätt.
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
ms.openlocfilehash: 66b881ab13e27ee457af4fa1bafb82ad14e9674d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631690"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Installera och använda din StorSimple-enhet på ett säkert sätt
![Varningsikon](./media/storsimple-safety/IC740879.png)
![läsa säkerhet Observera ikonen](./media/storsimple-safety/IC740885.png) **läsa INFORMATION om säkerhet och hälsa**

Läs all säkerhet och hälsa information i den här artikeln gäller för din Microsoft Azure StorSimple-enhet. Behåll alla utskrivna stödlinjer som medföljer din StorSimple-enhet för framtida bruk. Det gick inte att följa anvisningarna och korrekt ställa in, använder och hand för den här produkten kan öka risken för allvarlig skada eller död eller skada till enheterna. En [nedladdningsbar version av den här guiden](https://www.microsoft.com/download/details.aspx?id=44233) är också tillgänglig.

## <a name="safety-icon-conventions"></a>Ikonen konventioner för säkerhet
Här följer de ikoner som du hittar när du granskar de säkerhetsåtgärder som observeras när du konfigurerar och kör din Microsoft Azure StorSimple-enhet.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Ikon för risk](./media/storsimple-safety/IC740879.png) **FARA!** |Anger en farlig situation som, om inte undvikas leder död eller allvarlig skada. Den här signalen word är att begränsas i extrema fall. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!** |Anger en farlig situation som, om inte undvikas kan resultera i död eller allvarlig skada. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!** |Anger en farlig situation som, om inte undvikas kan resultera i att mindre eller måttlig skada. |
| ![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:** |Anger information som anses viktiga, men inte risk-relaterade. |
| ![Ikon för elektriska chocka](./media/storsimple-safety/IC740882.png) **elektriska chocka risk** |Hög spänning |
| ![Kraftiga ikonen](./media/storsimple-safety/IC740883.png) **stor vikt** | |
| ![Ingen användare som är användbara delar ikonen](./media/storsimple-safety/IC740879.png) **inga användbara delar användare** |Inte kommer åt såvida inte korrekt utbildade. |
| ![Läsa säkerhet Observera ikonen](./media/storsimple-safety/IC740885.png)**läsa alla instruktioner först** | |
| ![Varningsikon tipsikon](./media/storsimple-safety/IC740886.png) **tips risk** | |

## <a name="handling-precautions"></a>Hantering av vissa åtgärder
![Varningsikon](./media/storsimple-safety/IC740879.png) ![kraftiga ikonen](./media/storsimple-safety/IC740883.png) **varning!** 

Att minska risken för skador:

* Ett fullständigt konfigurerad hölje kan väga upp till 32 kg (70 lbs); Försök inte att flytta den själv.
* Innan du flyttar höljet ska du alltid kontrollera att det finns två personer att hantera vikten. Tänk på att en person försöker flytta den här vikt kan klara skador.
* Lyft inte höljet av handtagen på ström och kylning moduler (PCMs) finns på baksidan av enheten. Dessa är inte utformade för att dra vikten.

## <a name="connection-precautions"></a>Försiktighetsåtgärder för anslutning
![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektriska chocka ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska sannolikheten för att skada eller elektriska chocka död:

* När drivs av flera AC-källor och kopplar du från alla leverans-ström för fullständig isolering.
* Koppla permanent från enheten innan du flyttar den eller om du tror att det är skadad på något sätt.
* Tillhandahålla en säker elektriska earth anslutning till strömförsörjning strömkablar. Kontrollera att den erfarenhet av att arbeta på höljet uppfyller kraven för nationell och lokal innan du tillämpar power.
* Se till att power-anslutningen kopplas alltid före borttagningen av en PCM från höljet.
* Tanke på att ansluta på strömförsörjning strömsladd huvudsakliga koppla från enheten, kontrollera att uttag finns nära utrustningen och är lättillgängliga.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektriska chocka ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska sannolikheten för överhettning eller utlöses från elektriska anslutningar:

* Ge en lämplig strömkälla elektriska överlagring skydd att uppfylla de krav som anges i den tekniska specifikationen.
* Använd inte bifurcated strömkablar (”Y” leads).
* För att följa den tillämpliga säkerhet, utsläpp och termiska krav, inga försättsblad ska tas bort och alla fack måste fyllas i med plugin-program eller enhet tomma värden.
* Se till att utrustningen som används på ett sätt som anges av tillverkaren. Om den här utrustning används på ett sätt som inte har angetts av tillverkaren skrivas skyddet som utrustningen som.

![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För att fungera korrekt din utrustning och för att förhindra produkten:

* Det finns RJ45-portar på baksidan av enheten för en Ethernet-anslutning. Dessa måste inte vara ansluten till ett nätverk av telekommunikation.
* Glöm inte att installera enheten i ett rack som kan hantera en kylning fram till bak-design.
* Alla plugin-program och tom nivåer är en del av systemomslutningen. Dessa måste endast tas bort när en ersättning kan läggas till direkt. Systemet måste inte köras utan att alla moduler eller tomma värden på plats.

## <a name="rack-system-precautions"></a>Rack system försiktighetsåtgärder
De följande säkerhetskraven måste beaktas när du har monterat enheten i ett rack kabinettfil.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![tipsikon risk](./media/storsimple-safety/IC740886.png) **varning!**

Att minska risken för skador från ett tips över:

* Rack designen ska ha stöd för installerade höljena totalvikt och omfatta stabiliserande funktioner som är lämpliga för att förhindra att racket vältning eller som läggs över under installationen eller normal användning.
* Vid inläsning av ett rack, fylls rack nedifrån och Töm uppifrån ned.
* Dra inte mer än en bilaga från racket åt gången för att undvika risken för rack toppling.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektriska chocka ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska sannolikheten för att skada eller elektriska chocka död:

* Racket bör ha en säker elektriska distributionssystem. Den måste ge över aktuella skydd för höljet och måste inte vara överbelastad med det totala antalet höljen installerad. Den elektriska ström för förbrukning klassificeringen som visas på märkskylten skall observeras.
* Det elektriska distributionssystemet måste ange en tillförlitlig grunden för varje kabinett racken.
* Utformningen av elektriska distributionssystem måste ta hänsyn till det totala grunden läckaget aktuella från alla strömkällor i alla höljen. Observera att varje strömförsörjning i varje kabinett har en grunden läckage ström på 1.0 mA maximalt på 60 Hz 264 v. Racket kan kräva märkning med ”hög LÄCKAGE aktuella. Grunden (earth)-anslutning krävs innan du ansluter en tillgång ”.
* Rack, när den konfigurerats med höljena, måste uppfylla säkerhetskraven i UL 60950-1 och IEC 60950-1/EN 60950-1.

![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För rätt kylning rack systemets:

* Kontrollera att designen rack gör hänsyn maximala höljet fungerar temperatur av 35 grader Celsius (95 grader Fahrenheit).
* Systemet drivs med low-pressure, bakre avgaser installation (tryck till skapare rack dörrar och hinder högst 5 Pascal [0,5 mätare]).

## <a name="power-cooling-module-pcm-precautions"></a>Power kylning modulen (PCM) försiktighetsåtgärder
Enheten är avsedda att fungera med två PCMs. Var och en av PCMs har en strömförsörjning och fläktar med dubbla axlar. Under ett viktigt villkor kan systemet till ett fel på en strömförsörjning samtidigt som den fortfarande normal drift. Två PCMs (och därför strömförsörjning) måste alltid vara installerad. En enda PCM tillhandahåller redundant kraft. Därför kan fel på med ett enda PCM leda driftavbrott eller förlust av data.

![Varningsikon](./media/storsimple-safety/IC740879.png) ![elektriska chocka ikonen](./media/storsimple-safety/IC740882.png) **varning!**

Att minska sannolikheten för att skada eller elektriska chocka död:

* Ta inte bort försättsbladen från PCM. Det finns en risk för electric chocka i. Att returnera PCM och få en ny [kontakta Microsoft Support](storsimple-contact-microsoft-support.md).

![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

För att fungera korrekt din utrustning och för att förhindra produkten:

* Du måste ersätta misslyckade PCM inom 24 timmar. När en PCM tas bort för ersättning måste ersättningen slutföras inom 10 minuter efter borttagningen.
* Ta inte bort en PCM såvida inte en ersättning kan installeras omedelbart. Höljet köras inte utan alla moduler på plats.

## <a name="electrostatic-discharge-esd-precautions"></a>Elektrostatisk utsläpp (ESD) försiktighetsåtgärder
![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

Observera följande ESD-relaterade åtgärder.

* Se till att du har installerat och kontrolleras av en lämplig antistatiska handleden eller Ankelstövlar bandet.
* Se alla konventionella ESD försiktighetsåtgärder vid hantering av moduler och komponenter.
* Undvik kontakt med bakplan komponenter och modulen kopplingar.
* ESD skada omfattas inte av garanti.

## <a name="battery-disposal-precautions"></a>Batteri avyttring försiktighetsåtgärder
Strömförsörjningen använder en särskild batteri för att skydda innehållet i minne under tillfälligt, kortsiktig strömavbrott. Batteriet sitter i PCM. Tänk på följande information om batteriet.

![Varningsikon](./media/storsimple-safety/IC740879.png) **varning!**

Att minska risken för shorts, brand, explosion, skador eller död:

* Avyttra används batterier i enlighet med nationell/regional föreskrifter.
* Dela upp, krossa, eller värma över 60 grader Celsius (140 grader Fahrenheit) eller inte förbränning. Ersätt PCM-batteri med ett angivna batteri. Användning av en annan batteri kan utgöra en risk för brand eller explosion.
* Använd skyddande linjeslut på batterierna om dessa tas bort från strömförsörjningen.

![Observera ikonen](./media/storsimple-safety/IC740881.png) **meddelande:**

När leverans eller transport annars batterierna med flyg, följ IATA Lithium batteri vägledningen på [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

När du har granskat dessa meddelanden om säkerhet, är nästa steg att packa upp, racka och kabelanslut din enhet.

## <a name="next-steps"></a>Nästa steg
* För en 8100-enhet går du till [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md).
* För 8600-enhet, gå till [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).

