---
title: Installera och använda StorSimple-enheten på ett säkert sätt
description: Beskriver säkerhetskonventioner, riktlinjer och överväganden och förklarar hur du installerar och använder Din StorSimple-enhet på ett säkert sätt.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: dae6d535-1ca2-4d2b-b221-6819043aa068
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: alkohli
ms.openlocfilehash: 480875c17d5ff5bb5c0d42d827b5477f45bf30f4
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396470"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Installera och använda Din StorSimple-enhet på ett säkert sätt

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

![](./media/storsimple-safety/IC740879.png)
![Varningsikon Läs](./media/storsimple-safety/IC740885.png) säkerhetsmeddelande ikon **LÄS SÄKERHETS- OCH HÄLSOINFORMATION**

Läs all arbetsmiljöinformation i den här artikeln som gäller för din Microsoft Azure StorSimple-enhet. Behåll alla utskrivna guider som levereras med Din StorSimple-enhet för framtida referens. Underlåtenhet att följa instruktionerna och korrekt ställa in, använda och ta hand om denna produkt kan öka risken för allvarliga skador eller dödsfall, eller skador på enheten eller enheterna. En [nedladdningsbar version av den här guiden](https://www.microsoft.com/download/details.aspx?id=44233) finns också tillgänglig.

## <a name="safety-icon-conventions"></a>Konventioner om säkerhetsikon
Här är ikonerna som du hittar när du granskar de säkerhetsåtgärder som ska iakttas när du konfigurerar och kör din Microsoft Azure StorSimple-enhet.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Fara](./media/storsimple-safety/IC740879.png) Ikon **FARA!** |Indikerar en farlig situation som, om den inte undviks, kommer att leda till dödsfall eller allvarlig skada. Detta signalord ska begränsas till de mest extrema situationerna. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **VARNING!** |Indikerar en farlig situation som, om den inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Varningsikon](./media/storsimple-safety/IC740879.png) **VARNING VARNING!** |Anger en farlig situation som, om den inte undviks, kan leda till mindre eller måttliga skador. |
| ![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:** |Anger information som anses viktig, men inte farorelaterad. |
| ![Elektrisk stöt](./media/storsimple-safety/IC740882.png) Ikon **Elektrisk stöt Fara** |Hög spänning |
| ![Tung vikt](./media/storsimple-safety/IC740883.png) Ikon **tung vikt** | |
| ![Ingen användarservicebar](./media/storsimple-safety/IC740879.png) **reservdelsikon Inga delar som kan servas av användaren** |Få inte åtkomst om inte korrekt utbildad. |
| ![Läs säkerhetsmeddelande ikon](./media/storsimple-safety/IC740885.png)**Läs alla instruktioner först** | |
| ![Tips Hazard](./media/storsimple-safety/IC740886.png) Icon **Tips Hazard** | |

## <a name="handling-precautions"></a>Försiktighetsåtgärder vid hantering
![Varning](./media/storsimple-safety/IC740879.png) ![ikon](./media/storsimple-safety/IC740883.png) tung vikt Ikon **VARNING!** 

För att minska risken för skador:

* En fullt konfigurerad kapsling kan väga upp till 32 kg. försök inte lyfta den själv.
* Innan du flyttar inneslutningen ska du alltid se till att två personer är tillgängliga för att hantera vikten. Tänk på att en person som försöker lyfta denna vikt kan upprätthålla skador.
* Lyft inte höljet med handtagen på de power- och kylmoduler (PCM) som sitter på enhetens baksida. Dessa är inte utformade för att ta vikten.

## <a name="connection-precautions"></a>Försiktighetsåtgärder vid anslutning
![](./media/storsimple-safety/IC740879.png) ![Varningsikon elektrisk](./media/storsimple-safety/IC740882.png) stöt ikon **VARNING!**

För att minska risken för skador, elektriska stötar eller dödsfall:

* När du drivs av flera ac-källor, koppla bort all strömförsörjning för fullständig isolering.
* Koppla ur enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.
* Ge en säker elektrisk jordanslutning till nätsladdarna. Kontrollera att jordningen av inneslutningen uppfyller de nationella och lokala kraven innan du använder strömmen.
* Se till att strömanslutningen alltid kopplas bort innan en PCM tas bort från höljet.
* Med tanke på att kontakten på nätsladden är huvud frånkopplingsenheten, se till att uttagen är placerade nära utrustningen och är lättillgängliga.

![](./media/storsimple-safety/IC740879.png) ![Varningsikon elektrisk](./media/storsimple-safety/IC740882.png) stöt ikon **VARNING!**

För att minska risken för överhettning eller brand från de elektriska anslutningarna:

* Förse en lämplig strömkälla med elektriskt överbelastningsskydd för att uppfylla de krav som anges i den tekniska specifikationen.
* Använd inte bifurcated nätsladdar ("Y" ledningar).
* För att uppfylla tillämpliga säkerhets-, utsläpps- och värmebehov bör inga lock tas bort och alla fack måste fyllas med plug-in-moduler eller drivämnen.
* Se till att utrustningen används på ett sätt som anges av tillverkaren. Om denna utrustning används på ett sätt som inte anges av tillverkaren, kan det skydd som utrustningen tillhandahåller försämras.

![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:**

För korrekt användning av din utrustning och för att förhindra produktskador:

* RJ45-portarna på baksidan av enheten är endast för en Ethernet-anslutning. Dessa får inte vara anslutna till ett telenät.
* Var noga med att installera enheten i ett rack som rymmer en framåt-mot-rygg kylning design.
* Alla plug-in moduler och tomma plattor är en del av systemets kapsling. Dessa får endast tas bort när en ersättning omedelbart kan läggas till. Systemet får inte köras utan att alla moduler eller ämnen är på plats.

## <a name="rack-system-precautions"></a>Försiktighetsåtgärder för racksystem
Följande säkerhetskrav måste beaktas när du monterar enheten i ett rackskåp.

![](./media/storsimple-safety/IC740879.png) ![Varningsikon tips](./media/storsimple-safety/IC740886.png) fara ikon **VARNING!**

För att minska risken för skada från en välta:

* Rackkonstruktionen bör stödja den totala vikten på de installerade kapslingarna och bör innehålla stabiliseringsfunktioner som är lämpliga för att förhindra att racket tippar eller skjuts över under installation eller normal användning.
* När du laddar ett rack fyller du racket nedifrån och upp och töms uppifrån och ner.
* Skjut inte ut mer än ett hölje ur racket åt gången för att undvika risken för att racket välter.

![](./media/storsimple-safety/IC740879.png) ![Varningsikon elektrisk](./media/storsimple-safety/IC740882.png) stöt ikon **VARNING!**

För att minska risken för skador, elektriska stötar eller dödsfall:

* Racket ska ha ett säkert eldistributionssystem. Den måste ge överströmsskydd för inneslutningen och får inte överbelastas av det totala antalet installerade kapslingar. Den elektriska strömförbrukningsklass som visas på märkskylten bör observeras.
* Det elektriska distributionssystemet måste ge en tillförlitlig grund för varje kapsling i racket.
* Utformningen av det elektriska distributionssystemet måste ta hänsyn till den totala markläckageströmmen från alla nätaggregat i alla kapslingar. Observera att varje strömförsörjning i varje hölje har en markläckageström på högst 1,0 mA vid 60 Hz, 264 volt. Racket kan kräva märkning med "HÖG LÄCKAGESTRÖM. Jordanslutning är nödvändig innan en matning ansluts."
* Racket måste, när det är konfigurerat med kapslingarna, uppfylla säkerhetskraven i UL 60950-1 och IEC 60950-1/EN 60950-1.

![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:**

För korrekt kylning av ditt racksystem:

* Se till att rackdesignen tar hänsyn till den maximala inneslutningen som använder omgivningstemperaturen på 35 grader Celsius (95 grader Fahrenheit).
* Systemet drivs med lågtrycks-, bakavgasinstallation (mottryck skapat av rackdörrar och hinder som inte överstiger 5 Pascal [0,5 mm vattenmätare]).

## <a name="power-cooling-module-pcm-precautions"></a>Försiktighetsåtgärder för power cooling-modul (PCM)
Enheten är utformad för att fungera med två PCMs. Var och en av PCM har en strömförsörjning och en tvåaxlig fläkt. Under ett kritiskt tillstånd möjliggör systemet ett fel på en strömförsörjning samtidigt som normal drift fortsätter. Två PCMs (och därmed nätaggregat) måste alltid installeras. En enda PCM ger inte redundant ström. Därför kan felet på ens en PCM resultera i driftstopp eller möjlig dataförlust.

![](./media/storsimple-safety/IC740879.png) ![Varningsikon elektrisk](./media/storsimple-safety/IC740882.png) stöt ikon **VARNING!**

För att minska risken för skador, elektriska stötar eller dödsfall:

* Ta inte bort kåporna från PCM. Det finns risk för elektriska stötar inuti. Kontakta [Microsoft Support](storsimple-contact-microsoft-support.md)om du vill returnera PCM och få en ersättning.

![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:**

För korrekt användning av din utrustning och för att förhindra produktskador:

* Du måste ersätta den misslyckade PCM inom 24 timmar. När en PCM har tagits bort för utbyte måste ersättningen slutföras inom 10 minuter efter borttagningen.
* Ta inte bort en PCM om inte en ersättning kan installeras omedelbart. Kapslingen får inte användas utan alla moduler på plats.

## <a name="electrostatic-discharge-esd-precautions"></a>Försiktighetsåtgärder vid elektrostatisk urladdning (ESD)
![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:**

Beakta följande ESD-relaterade försiktighetsåtgärder.

* Se till att du har installerat och kontrollerat en lämplig antistatisk handled eller fotled rem.
* Beakta alla konventionella ESD försiktighetsåtgärder vid hantering av moduler och komponenter.
* Undvik kontakt med bakplanskomponenter och modulkontakter.
* ESD-skador täcks inte av garantin.

## <a name="battery-disposal-precautions"></a>Försiktighetsåtgärder för kassering av batterier
Strömförsörjningen använder ett speciellt batteri för att skydda innehållet i minnet vid tillfälliga, kortsiktiga strömavbrott. Batteriet sitter i PCM. Ha följande information i åtanke om batteriet.

![Varningsikon](./media/storsimple-safety/IC740879.png) **VARNING!**

För att minska risken för shorts, brand, explosion, skada eller dödsfall:

* Kassera använda batterier i enlighet med nationella/regionala föreskrifter.
* Ta inte isär, krossa eller värm över 60 grader Celsius (140 grader Fahrenheit) eller förbränn. Byt ut PCM-batteriet mot endast ett medföljande batteri. Användning av ett annat batteri kan medföra risk för brand eller explosion.
* Använd skyddslocken på batterierna om dessa tas bort från strömförsörjningen.

![Meddelande](./media/storsimple-safety/IC740881.png) Ikon **MEDDELANDE:**

När du skickar eller på annat sätt transporterar batterierna med flyg ska du följa IATA:s vägledningsdokument om litiumbatteri som finns på[https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

När du har granskat dessa säkerhetsmeddelanden är nästa steg att packa upp, rack och kabel enheten.

## <a name="next-steps"></a>Nästa steg
* För en 8100-enhet går du till [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md).
* För en 8600-enhet går du till [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).

