---
title: Säkerhet för Azure Data Box | Microsoft Docs
description: Beskriver hur du på ett säkert sätt installera och använda Azure Data Box beskrivs konventioner för säkerhet, riktlinjer och överväganden.
services: databox
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f933da6d534d13a989b21b9cac6b47b41a81d79c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961815"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Installera och använda Azure Data box-enhet på ett säkert sätt
![Varningsikon](./media/data-box-safety/warning_icon.png)
![läsa säkerhet Observera ikonen](./media/data-box-safety/read_safety_and_health_information_icon.png) **läsa INFORMATION om säkerhet och hälsa**

Läs alla säkerhetsinformation i den här artikeln innan du använder Azure Data Box. Det gick inte att följa anvisningarna kan leda fire, electric chocka eller andra skador eller skada till dina egenskaper.

## <a name="safety-icon-conventions"></a>Ikonen konventioner för säkerhet
Här följer ikonerna att du kan hitta när du granskar de säkerhetsåtgärder som observeras när du konfigurerar och kör din Data Box.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Ikon för risk](./media/data-box-safety/warning_icon.png) **FARA!** |Anger en farlig situation som, om inte undvikas leder död eller allvarlig skada. Den här signalen word är att begränsas i extrema fall. |
| ![Varningsikon](./media/data-box-safety/warning_icon.png) **varning!** |Anger en farlig situation som, om inte undvikas kan resultera i död eller allvarlig skada. |
| ![Varningsikon](./media/data-box-safety/warning_icon.png) **varning!** |Anger en farlig situation som, om inte undvikas kan resultera i att mindre eller måttlig skada. |
| ![Observera ikonen](./media/data-box-safety/notice_icon.png) **meddelande:** |Anger information som anses viktiga, men inte risk-relaterade. |
| ![Ikon för elektriska chocka](./media/data-box-safety/electrical_shock_hazard_icon.png) **elektriska chocka risk** |Högre spänningen. |
| ![Kraftiga ikonen](./media/data-box-safety/heavy_weight_hazard_icon.png) **stor vikt** | |
| ![Ingen användare som är användbara delar ikonen](./media/data-box-safety/no_user_serviceable_parts_icon.png) **inga användbara delar användare** |Inte kommer åt såvida inte korrekt utbildade. |
| ![Läsa säkerhet Observera ikonen](./media/data-box-safety/read_safety_and_health_information_icon.png) **läsa alla instruktioner först** | |
| ![Varningsikon tipsikon](./media/data-box-safety/tip_hazard_icon.png) **tips risk** | |

## <a name="handling-precautions"></a>Hantering av vissa åtgärder

![Varningsikon](./media/data-box-safety/warning_icon.png) ![elektriska chocka ikonen](./media/data-box-safety/electrical_shock_hazard_icon.png)![ingen funktionsduglig användare delar ikonen](./media/data-box-safety/no_user_serviceable_parts_icon.png) **varning** 

* Granska den *som fick* enhet för skador. Om enheten höljet har skadats, kontaktar du Microsoft Support om du vill få en ny. Försök inte att använda enheten. 
* Enheten är utrustad med manipuleringssäker skruvar. Om du misstänker att enheten är fel, kontakta Microsoft Support om du vill få en ny. Försök inte att hantera enheten. 
* Enheten innehåller inga användare funktionsduglig delar. Farlig spänning, aktuell och energi-nivåer finns i. Öppna inte. Gå tillbaka enheten till Microsoft för att underhålla.

![Varningsikon](./media/data-box-safety/warning_icon.png) ![kraftiga ikonen](./media/data-box-safety/heavy_weight_hazard_icon.png) **varning!** 

* Ett fullständigt konfigurerad hölje kan väga upp till 22.7 kg (50 lbs); Försök inte att flytta den själv.
* Innan du flyttar höljet ska du alltid kontrollera att det finns två personer att hantera vikten. Tänk på att en person försöker flytta den här vikt kan klara skador.


![Varningsikon](./media/data-box-safety/warning_icon.png) ![tipsikon risk](./media/data-box-safety/tip_hazard_icon.png) **varning!**
* Placera enheten på en fast, hård och stabila yta att undvika en risk för tips.
* Rackmonterad utrustning ska inte användas som hyllor eller utrymmet. Placera inte Data Box ovanpå rackmonterad utrustning. Att lägga till alla typer av belastningen till en utökad rackmonterade enhet kan du skapa en risk för tips som kan leda till skada, död eller produkten skadas.

![Varningsikon](./media/data-box-safety/warning_icon.png) **varning!**

* Ställa in enheten i en arbetsyta, vilket ger tillräcklig air omsättning runt enheten.
* Installera enheten i en temperatur-kontrollerade inom området kostnadsfritt ledande främmande ämnen och tillåter tillräcklig air omsättning runt enheten.
* Skydda från källor med en flytande och mycket hög-miljöer.


## <a name="electrical-precautions"></a>Elektriska försiktighetsåtgärder

![Varningsikon](./media/data-box-safety/warning_icon.png) ![elektriska chocka ikonen](./media/data-box-safety/electrical_shock_hazard_icon.png) **varning!**

* Tillhandahålla en säker elektriska earth anslutning till strömförsörjning kontakten. AC-kabel har tre ledare erfarenhet av att arbeta-plugin (en kontakt som har erfarenhet av att arbeta PIN-kod). Det här plugin passar bara ett förankrad AC uttag. Inte att motverka syftet med erfarenhet av att arbeta PIN-kod.
* Tanke på att plugin på strömförsörjning strömsladd är huvudsakliga koppla från enheten, kontrollera att uttag finns nära enheten och är lättillgängliga.
* Koppla från strömsladden (genom att hämta plugin, inte kabel) och koppla från alla kablar om någon av följande villkor föreligger:

    - Strömsladd eller plugin blir slitna annars skadad.
    - Du spiller något till enheten gemener och versaler.
    - Enheten visas regn eller överflödig vätska.
    - Enheten har tagits bort och enheten gemener och versaler är skadad.
    - Du misstänker att enheten behöver service eller reparation.
* Koppla permanent från enheten innan du flyttar den eller om du tror att det är skadad på något sätt.
* Ange en lämplig strömkälla elektriska överlagring skydd att uppfylla följande power specifikationer:

    - Spänning: 100 V AC till 240 V AC
    - Aktuellt: 6, maximalt
    - Frekvens: 50 Hz till 60 Hz

![Varningsikon](./media/data-box-safety/warning_icon.png) **varning:**

* Den här enheten innehåller mynt cellsbatterier. Försök inte att hantera enheten. Batterier i den här enheten är inte funktionsduglig användare. 
* **För tjänsten personal endast**: Risk för Explosion om batteri ersätts av en felaktig typ. Ta bort används batterier enligt anvisningarna.

![Observera ikonen](./media/data-box-safety/notice_icon.png) **meddelande:**

För att fungera korrekt för din enhet och för att förhindra produkten:

* Kontrollera att klient- och bakdörrar är helt öppen medan enheten är igång.

## <a name="regulatory-information"></a>Gällande föreskrifter

Det här avsnittet innehåller regler för Azure Data Box, föreskrifter modellnummer DB010.

Den här enheten är:

- Utvärderas som Information Technology utrustning (OD), avsedda att fungera i en typisk rummet miljö. Om den här produkten för andra miljöer lämpliga kan kräva ytterligare utvärdering.
- Avsedd för användning med NRTL visas (UL, CSA, ETL, etc.) och IEC/EN 60950-1 eller IEC/EN 62368-1-kompatibel (CE markerad) informationsteknik utrustning.
- Utformad för att fungera i följande miljö. 
    - Temperatur vid drift: 50° 95° f (10° till 35° C)
    - Temperatur vid förvaring:-4 ° till 122 ° F (20 ° till 50 ° C)
    - Relativ fuktighet: 15 till 85% (icke-kondenserande) 
    - Driva höjd: testats upp till 6500 meter 0 till 2 000 mätare

Strömförsörjningen klassificeringar, finns enheten Klassificeringsetiketten medföljer enheten. 

![Observera ikonen](./media/data-box-safety/notice_icon.png) **meddelande:** 

Eller ändringar som gjorts till den enhet som inte uttryckligen har godkänts av Microsoft kan annullera användarens behörighet att använda enheten.

**Kanada och USA:**

![Observera ikonen](./media/data-box-safety/notice_icon.png) **meddelande:** 

Den här utrustning har testats och efterlevnaden gränser för en klass A digital enhet, i enlighet med 15 tillhör FCC-regler. Dessa gränser är utformade för att ge rimliga skydd mot skadlig störning när utrustningen som körs i en kommersiell miljö. Den här utrustning genererar, använder, och kan bestråla alternativknapp frekvensen energi och, om inte installeras och användas i enlighet med bruksanvisningen, som kan orsaka skadlig störning radio kommunikation. Användning av den här utrustningen i en lokal område är kan orsaka skadlig störning i vilket fall användaren kommer att behöva korrigera störningar på sin egen bekostnad.

Den här enheten uppfyller del 15 av FCC regler och branschen Kanada licens undantagna RSS standarder. Åtgärden lyder under följande villkor: (1) den här enheten inte kan orsaka skadlig störning och (2) den här enheten måste godkänna alla störningar som tagits emot, inklusive störningar som kan orsaka oönskade driften av enheten.

![Kanada](./media/data-box-safety/canada.png)

KAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft sätt, Redmond, WA 98052, USA.

USA: (800) 426-9400

Kanada: (800) 933-4750

**EUROPEISKA UNIONEN:**

En kopia av EU: S deklaration om överensstämmelse finns här.

![Varningsikon](./media/data-box-safety/warning_icon.png) **varning:** 

Det här är en klass A-produkt. Den här produkten kan orsaka radiostörningar som fallet användaren kanske måste vidta lämpliga åtgärder i en inrikes miljö.

**Borttagning av spill batterier och elektriska och elektroniska produkter:**

![Ikon för borttagning av batteri](./media/data-box-safety/battery_disposal_icon.png)

Den här symbolen på produkten eller dess batterier eller dess förpackning innebär att den här produkten och eventuella batterier som den innehåller inte måste avyttras med din hushållens spill. I stället är det ditt ansvar att överlämna detta till en tillämplig samling återanvändning av batterier och elektriska och elektroniska produkter. Samlingen separat och återvinning kommer hjälpa till att spara naturresurser och förhindra eventuella negativa konsekvenser för människors hälsa och miljön på grund av möjlig förekomst av farliga ämnen i batterier och elektriska och elektroniska utrustning, som kan ha orsakats av olämpliga borttagning. Mer information om var du vill lämna in dina batterier och elektriska och elektroniska avfall Kontakta din lokala stad/kommuner office, hushållens avfallshantering tjänsten eller verkstaden där du har köpt den här produkten. Kontakta *erecycle@microsoft.com* för mer information om WEEE.

Den här produkten innehåller mynt cell battery(ies).

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

Telefonnummer: + 353 1 295 3826

Faxnummer: + 353 1 706 4110 

**Japan**

![Japan](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

När du har granskat dessa meddelanden om säkerhet, kan du konfigurera och kabelanslut din enhet.

## <a name="next-steps"></a>Nästa steg

* [Ansluta och ansluta din Data box-enhet](data-box-deploy-set-up.md)


