---
title: Säkerhet för din Azure Data Box | Microsoft-dokument
description: Beskriver säkerhetskonventioner, riktlinjer och överväganden och förklarar hur du installerar och använder din Azure Data Box på ett säkert sätt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 08/13/2019
ms.author: alkohli
ms.openlocfilehash: db6e2eadbf19d78a203a4d4ba6111ad88430b192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68985952"
---
# <a name="safely-install-and-operate-your-azure-data-box"></a>Installera och använda din Azure Data Box på ett säkert sätt
![](./media/data-box-safety/warning_icon.png)
![Varningsikon Läs](./media/data-box-safety/read_safety_and_health_information_icon.png) säkerhetsmeddelande ikon **LÄS SÄKERHETS- OCH HÄLSOINFORMATION**

Läs all säkerhetsinformation i den här artikeln innan du använder Azure Data Box. Underlåtenhet att följa instruktionerna kan leda till brand, elektriska stötar eller andra skador eller skador på dina egenskaper.

## <a name="safety-icon-conventions"></a>Konventioner om säkerhetsikon
Här är ikonerna som du hittar när du granskar de säkerhetsåtgärder som ska iakttas när du ställer in och kör din databox.

| Ikon | Beskrivning |
|:--- |:--- |
| ![Fara](./media/data-box-safety/warning_icon.png) Ikon **FARA!** |Indikerar en farlig situation som, om den inte undviks, kommer att leda till dödsfall eller allvarlig skada. Detta signalord ska begränsas till de mest extrema situationerna. |
| ![Varningsikon](./media/data-box-safety/warning_icon.png) **VARNING!** |Indikerar en farlig situation som, om den inte undviks, kan leda till dödsfall eller allvarlig skada. |
| ![Varningsikon](./media/data-box-safety/warning_icon.png) **VARNING VARNING!** |Anger en farlig situation som, om den inte undviks, kan leda till mindre eller måttliga skador. |
| ![Meddelande](./media/data-box-safety/notice_icon.png) Ikon **MEDDELANDE:** |Anger information som anses viktig, men inte farorelaterad. |
| ![Elektrisk stöt](./media/data-box-safety/electrical_shock_hazard_icon.png) Ikon **Elektrisk stöt Fara** |Hög spänning. |
| ![Tung vikt](./media/data-box-safety/heavy_weight_hazard_icon.png) Ikon **tung vikt** | |
| ![Ingen användarservicebar](./media/data-box-safety/no_user_serviceable_parts_icon.png) **reservdelsikon Inga delar som kan servas av användaren** |Få inte åtkomst om inte korrekt utbildad. |
| ![Läs säkerhetsmeddelande](./media/data-box-safety/read_safety_and_health_information_icon.png) ikon **Läs alla instruktioner först** | |
| ![Tips Hazard](./media/data-box-safety/tip_hazard_icon.png) Icon **Tips Hazard** | |

## <a name="handling-precautions"></a>Försiktighetsåtgärder vid hantering

![](./media/data-box-safety/warning_icon.png) ![Varningsikon elektrisk](./media/data-box-safety/electrical_shock_hazard_icon.png)![stöt ikon](./media/data-box-safety/no_user_serviceable_parts_icon.png) Ingen användarservicebar delar Ikon **VARNING VARNING** 

* Kontrollera den *mottagna* enheten för skador. Om enhetshöljet är skadat [kontaktar du Microsoft Support](data-box-disk-contact-microsoft-support.md) för att få en ersättning. Försök inte använda enheten. 
* Enheten är utrustad med manipuleringssäkra skruvar. Om du misstänker att enheten inte fungerar [kontaktar du Microsoft Support](data-box-disk-contact-microsoft-support.md) för att få en ersättare. Försök inte att serva enheten. 
* Enheten innehåller inga delar som kan servas av användaren. Farlig spänning, ström och energinivåer finns inuti. Öppna inte. Returnera enheten till Microsoft för service.

![Varning](./media/data-box-safety/warning_icon.png) ![ikon](./media/data-box-safety/heavy_weight_hazard_icon.png) tung vikt Ikon **VARNING!** 

* En fullt konfigurerad kapsling kan väga upp till 22,7 kg . försök inte lyfta den själv.
* Innan du flyttar inneslutningen ska du alltid se till att två personer är tillgängliga för att hantera vikten. Tänk på att en person som försöker lyfta denna vikt kan upprätthålla skador.


![](./media/data-box-safety/warning_icon.png) ![Varningsikon tips](./media/data-box-safety/tip_hazard_icon.png) fara ikon **VARNING!**
* Placera enheten på en plan, hård och stabil yta för att undvika en potentiell spetsrisk.
* Rackmonterad utrustning får inte användas som hyllor eller arbetsutrymmen. Placera inte databoxen ovanpå rackmonterad utrustning. Om du lägger till någon typ av last till en förlängd rackmonterad enhet kan det skapa en potentiell spetsfara som kan leda till personskador, dödsfall eller produktskador.

![Varningsikon](./media/data-box-safety/warning_icon.png) **VARNING!**

* Ställ in enheten på ett arbetsområde som möjliggör tillräcklig luftcirkulation runt enheten.
* Installera enheten i ett temperaturkontrollerat inomhusområde fritt från ledande föroreningar och möjliggör tillräcklig luftcirkulation runt enheten.
* Håll enheten borta från vätskekällor och alltför fuktiga miljöer.


## <a name="electrical-precautions"></a>Elektriska försiktighetsåtgärder

![](./media/data-box-safety/warning_icon.png) ![Varningsikon elektrisk](./media/data-box-safety/electrical_shock_hazard_icon.png) stöt ikon **VARNING!**

* Ge en säker elektrisk jordanslutning till nätsladden. Nätsladden har en jordkontakt med tre trådar (en kontakt som har jordningsstift). Denna kontakt passar endast ett jordat eluttag. Besegra inte syftet med jordningsstiftet.
* Med tanke på att kontakten på nätsladden är huvud frånkopplingsenheten, se till att uttagen är placerade nära enheten och är lättillgängliga.
* Koppla ur nätsladden (genom att dra i kontakten, inte sladden) och koppla bort alla kablar om något av följande villkor föreligger:

    - Nätsladden eller kontakten blir sliten eller på annat sätt skadad.
    - Du spiller något i enhetens hölje.
    - Enheten utsätts för regn eller fukt.
    - Enheten släpptes och enhetens hölje är skadat.
    - Du misstänker att enheten behöver service eller reparation.
* Koppla ur enheten permanent innan du flyttar den eller om du tror att den har skadats på något sätt.
* Ge en lämplig strömkälla med elektriskt överbelastningsskydd för att uppfylla följande effektspecifikationer:

    - Spänning: 100 V AC till 240 V AC
    - Ström: 6 A, max
    - Frekvens: 50 Hz till 60 Hz

![Varningsikon](./media/data-box-safety/warning_icon.png) **VARNING VARNING:**

* Den här enheten innehåller myntcellsbatterier. Försök inte att serva enheten. Batterier i den här enheten kan inte servas av användaren. 
* **Endast för servicepersonal**: Explosionsrisk om batteriet byts ut mot en felaktig typ. Kassera de använda batterierna enligt instruktionerna.

![Meddelande](./media/data-box-safety/notice_icon.png) Ikon **MEDDELANDE:**

För att enheten ska fungera korrekt och för att förhindra produktskador:

* Se till att fram- och bakdörrarna är helt öppna medan enheten är igång.

## <a name="regulatory-information"></a>Regleringsinformation

Det här avsnittet innehåller regulatorisk information för Azure Data Box, regulatoriskt modellnummer DB010.

Den här enheten är:

- Utvärderas som ITE (Information Technology Equipment), utformad för att fungera i en typisk datarumsmiljö. Produktens lämplighet för andra miljöer kan kräva ytterligare utvärdering.
- Utformad för användning med NRTL-listad (UL, CSA, ETL, etc.) och IEC/EN 60950-1 eller IEC/EN 62368-1-kompatibel (CE-märkt) INFORMATIONsteknikutrustning.
- Utformad för att fungera i följande miljö. 
    - Driftstemperatur: 50° till 95° F (10° till 35° C)
    - Förvaringstemperatur: -4° till 122° F (-20° till 50° C)
    - Relativ luftfuktighet: 15% till 85% (noncondensing) 
    - Höjd över havet vid drift: Testad upp till 6500 fot (0 meter till 2000 meter)

För klassificering av elförsörjning, se enhetens märketikett som medföljer enheten. 

![Meddelande](./media/data-box-safety/notice_icon.png) Ikon **MEDDELANDE:** 

Ändringar eller ändringar som gjorts på enheten som inte uttryckligen godkänts av Microsoft kan upphäva användarens behörighet att använda enheten.

**KANADA och USA:**

![Meddelande](./media/data-box-safety/notice_icon.png) Ikon **MEDDELANDE:** 

Denna utrustning har testats och befunnits uppfylla gränserna för en digital enhet av klass A, i enlighet med del 15 i FCC-reglerna. Dessa gränsvärden är utformade för att ge ett rimligt skydd mot skadliga störningar när utrustningen används i en kommersiell miljö. Denna utrustning genererar, använder och kan utstråla radiofrekvensenergi och kan, om den inte installeras och används i enlighet med bruksanvisningen, orsaka skadliga störningar i radiokommunikationen. Driften av denna utrustning i ett bostadsområde kommer sannolikt att orsaka skadliga störningar i vilket fall användaren kommer att krävas för att korrigera störningen på egen bekostnad.

Den här enheten uppfyller del 15 i FCC:s regler och bransch kanadas licensbefriade RSS-standard.This device complie with part 15 of the FCC Rules and Industry Canada license-exempt RSS standard(s). Användning är föremål för följande två villkor: (1) den här enheten får inte orsaka skadliga störningar, och (2) den här enheten måste acceptera alla mottagna störningar, inklusive störningar som kan orsaka oönskad användning av enheten.

![Kanada](./media/data-box-safety/canada.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.

Förenta staterna: (800) 426-9400

Kanada: (800) 933-4750

**EUROPEISKA UNIONEN:**

[Begär en kopia av EU:s försäkran om överensstämmelse](mailto:CSI_Compliance@microsoft.com).

![Varningsikon](./media/data-box-safety/warning_icon.png) **VARNING:** 

Detta är en klass A-produkt. I en inhemsk miljö kan denna produkt orsaka radiostörningar, i vilket fall användaren kan åläggas att vidta lämpliga åtgärder.

**Bortskaffande av förbrukade batterier och elektrisk och elektronisk utrustning:**

![Ikon för kassering av batterier](./media/data-box-safety/battery_disposal_icon.png)

Denna symbol på produkten eller dess batterier eller dess förpackning innebär att denna produkt och eventuella batterier som den innehåller inte får kastas tillsammans med hushållsavfallet. I stället är det ditt ansvar att lämna över detta till en lämplig återvinningsstation för batterier och elektrisk och elektronisk utrustning. Denna separata insamling och återvinning kommer att bidra till att bevara naturresurser och förhindra potentiella negativa konsekvenser för människors hälsa och miljön på grund av eventuell förekomst av farliga ämnen i batterier och elektriska och elektroniska utrustning, som kan orsakas av olämpligt bortskaffande. För mer information om var du kan släppa av dina batterier och elektriskt och elektroniskt avfall, kontakta ditt lokala stads-/kommunkontor, din hushållsavfallstjänst eller butiken där du köpte produkten. Kontakta *erecycle\@microsoft.com* för ytterligare information om WEEE.

Denna produkt innehåller myntcellsbatteri(er).

Microsoft Irland Sandyford Ind Est Dublin D18 KX32 IRL

Telefonnummer: +353 1 295 3826

Faxnummer: +353 1 706 4110 

**Japan**

![Japan](./media/data-box-safety/japan.png)

<!--**South Korea**

![South Korea](./media/data-box-safety/south-korea.png)

**China RoHS Hazardous Substances Table**

![China RoHS hazardous substances](./media/data-box-safety/south-korea.png)

**Taiwan**

![Taiwan](./media/data-box-safety/taiwan.png)-->

När du har granskat dessa säkerhetsmeddelanden kan du ställa in och kabela enheten.

## <a name="next-steps"></a>Nästa steg

* [Kabel och anslut din Data Box](data-box-deploy-set-up.md)


