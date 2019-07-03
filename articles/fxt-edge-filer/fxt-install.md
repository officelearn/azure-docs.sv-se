---
title: Självstudiekurs om hur du installerar en fysisk enhet i Azure FXT Edge Filer | Microsoft Docs
description: Packa upp, rack och kabel komponenten fysisk enhet i Microsoft Azure FXT Edge Filer hybrid storage cache
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ed9eca88e5ccc386b25acb95fa729a3cfb95cbd0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543485"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Självstudier: Install Azure FXT Edge Filer 

Den här självstudien beskrivs hur du installerar en nod för maskinvara för Azure FXT Edge Filer hybrid storage cache. Du måste installera minst tre maskinvarunoder för att skapa ett kluster i Azure FXT Edge Filer.

Installationsproceduren omfattar uppackning och rack montera enheten och koppla kabel hanteringsarmen (CMA) och ramens framsida. En separat självstudien beskrivs kopplar nätverkskablarna och anslutande power. 

Det tar ungefär en timme att installera en Filer för Azure FXT Edge-nod. 

Den här självstudiekursen beskrivs dessa installationssteg: 

> [!div class="checklist"]
> * Packa upp enheten
> * Montera enheten i ett rack
> * Installera på klientsidan ramen (valfritt)

## <a name="installation-prerequisites"></a>Installationskrav 

Innan du börjar måste du kontrollera att datacenter och rack som du använder har dessa funktioner:

* En tillgänglig 1U fack på racket där du vill montera enheten.
* AC-strömförsörjning och kylning system som uppfyller behoven hos Azure FXT Edge-Filer. (Läs [kraft och termiska specifikationer](fxt-specs.md#power-and-thermal-specifications) för hjälp med att planera och ändra storlek på installationen.)  

  > [!NOTE] 
  > Om du vill dra full nytta av två redundant strömförsörjning (PSUs), Använd kraftfördelningsenheter på två olika gren kretsar nätström. Läs [ansluta strömkablar](fxt-network-power.md#connect-power-cables) mer information.  

## <a name="unpack-the-hardware-node"></a>Packa upp noden maskinvara 

Varje nod i Azure FXT Edge Filer levereras i en enda ruta. Slutför stegen nedan för att packa upp en enhet.

1. Placera lådan på en plan, jämn yta.

2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna den. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.

3. Packa upp lådan. Kontrollera att den innehåller följande objekt:
   * En enda hölje FXT enhet
   * Två strömkablar
   * En ramens framsida och nyckel
   * En rail kit sammansättning
   * En kabel hanteringsarmen (CMA)
   * CMA installation instruktioner häfte
   * Rack installationen instruktioner häfte
   * Säkerhet, miljö och föreskrifter häfte

Om du inte fått alla objekt i listan, kontaktar du leverantören av enheten för support. 

Kontrollera att enheten har fått tillräckligt med tid att nå samma temperatur som rummet innan du installerar den eller aktiverar den. Om du märker kondensation på någon del av enheten kan du vänta minst 24 timmar innan du installerar.

Nästa steg är att rackmontera enheten.

## <a name="rack-the-device"></a>Rackmontera enheten

Filer för Azure FXT Edge-enheten måste installeras i en standard 19-tums rack. 

Azure FXT Edge Filer hybrid storage cache består av tre eller fler Filer för Azure FXT Edge-enheter. Upprepa installationsstegen rack för varje enhet som är del av systemet. 

### <a name="rack-install-prerequisites"></a>Rack installera nödvändiga komponenter

* Innan du kan läsa anvisningarna säkerhet i säkerhet, miljö och föreskrifter häftet som levererades med din enhet.

  > [!NOTE]
  > Använd alltid två personer lyftning noden, inklusive när du installerar den i ett rack eller ta bort den från racket. 

* Identifiera rail installationstyp som används med din utrustning rack. 
  * Kvadratisk eller runda hål snapin-modulen rack i Verktyg rail anvisningarna.
  * För flertrådade hål rack, följer du instruktionerna tooled rail. 
  
    Du måste ange åtta skruvar, typ 10-32, 12 – 24, M5 eller M6 för tooled spåret montera configuration. Skruvarna head diameter måste vara mindre än 10 mm (0,4 ”).

### <a name="identify-the-rail-kit-contents"></a>Identifiera rail kit innehållet

Leta upp komponenterna för att installera rail kit sammansättningen:

1. Dell ReadyRails av två A7-II som glidande rail sammansättningar (1)
1. Två anslutas och loopa remmar (2)

![Numrerade ritning av rail kit innehållet](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Rail sammansättningen - verktyg rails (kvadratisk hål eller runda hål rack)

Följ den här proceduren för att samla ihop och installera rails för rack med snapin-modulen kvadratisk eller runda hål. 

1. Placera vänster och höger rail slutet delar märkta **främre** mot aktiv. Placera varje slutpunkt del så att den platser i hål på de lodräta racklister framsida. (1)

2. Justera varje slutpunkt del i de nedre och övre hål Tor i området som du vill montera den. (2)

3. Engagera spåret backend-servern tills den fullständigt platser på lodrät rack flänsens och låset klickar på plats. Upprepa dessa steg om du vill placera och enhet klientdelen del på flänsens lodrät rack. (3)

> [!TIP]
> Ta bort rails, hämta knappen spärr versionen på slutet del mittpunkten (4) och ta bort varje rail.

![Diagram över installation och borttagning av verktyg rails med numrerade steg](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Rail sammansättningen - tooled rails (listvy hål rack)

Följ den här proceduren för att samla ihop och installera rails för rack med flertrådade hål.

1. Ta bort de PIN-koderna från främre och bakre montera hakparenteser med en fast spets för. (1)
1. Hämta och rotera rail spärr delprodukter om du vill ta bort dem från montering hakparenteser. (2)
1. Ansluta till vänster och höger montera rails till front lodrät racklister med hjälp av två par skruvar. (3)
1. Dra tillbaka vänster och höger hakparentes vidarebefordra mot de bakre lodrät racklister och bifoga dem med hjälp av två par skruvar. (4)

![Diagram över installation och borttagning av tooled rails med numrerade steg](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installera i racket

Följ dessa steg om du vill montera Filer för Azure FXT Edge-enhet i racket.

1. Hämta inre bild rails utanför racket tills de lås på plats. (1)
1. Leta upp bakre rail standoff på varje sida av enheten och sänka dem till bakre J-fack på bild-sammansättningar. (2) 
1. Om du roterar hela enheten nedåt tills alla rail standoffs har satts i J-platserna. (3)
1. Skicka enheten inåt tills Lås steg klickar du på på plats.
1. Tryck på knapparna bild versionen lås på båda rails (4) och dra enheten i racket.

![Installera system i ett rackdiagram med numrerade steg](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

Om du vill ta bort enheten från racket, följer du den här proceduren. 

1. Leta upp Lås reglage på sidorna av de inre rails (1).
2. Lås upp varje spak genom att rotera upp till dess version position (2).
3. Rapportelementen sidor av systemet ordentligt och dra den framåt tills rail standoffs är längst fram av J-platserna. Lyft systemet upp och från racket och placera den på en nivå yta (3).

![Bild av att ta bort ett system från rack, med numrerade steg](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Engagera slam spärr

1. Klientdelen som riktas mot, leta upp slam spärr (1) på endera sidan av systemet.
2. Lås engagera automatiskt när systemet pushas till racket. 

Släpp Lås när du tar bort systemet genom att hämta dem upp (2).

Valfritt hårda montering skruvar tillhandahålls för att skydda system till racket för leverans eller i miljöer från andra instabil. Hitta skruven under varje Lås och dra dem åt med ett #2 stjärnskruvmejsel (3).

![Numrerade illustration av engagerande och frisläppa slam spärr](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installera hanteringsarmen kabel 

Ett valfritt kabel hanteringsarmen (CMA) medföljer FXT Edge-Filer. Skriva ut anvisningar för att installera den finns i paketet. 

1. Packa upp och identifiera komponenterna i arm-kit kabel management:
   * CMA fack (1)
   * CMA (2)
   * Nylon kabel koppla omsluter (3)
   * CMA bilaga hakparenteser (4)
   * Status för indikatorn kabel (5) 

   > [!TIP] 
   > Om du vill skydda CMA för leverans i racket loopa koppla radbryter runt både korgar och fack och cinch dem ordentligt. Skydda CMA på det här sättet kommer även skydda ditt system i instabil miljöer.

   ![Bild av CMA delar](media/fxt-install/cma-kit-400.png)

2. Installera CMA fack.

   CMA systemfältet ger stöd och fungerar som en kvarhållande för CMA. 

   1. Justera och engagera båda sidor av systemfältet med hakparenteserna mottagare på rails inre kanter. 
   1. Skicka facket fram tills det klickar på plats. (1)
   1. Ta bort facket klämma knapparna spärr versionen mot mitten och hämta systemfältet utanför mottagare hakparenteser (2).

   ![Bild av CMA systemfältet installation](media/fxt-install/cma-tray-install-400.png)

3. Installera CMA bilaga hakparenteser. 

   > [!NOTE]
   >
   > * Du kan koppla CMA till höger eller vänster montera rail, beroende på hur du planerar att dirigera kablar från systemet. 
   > * För att underlätta för att montera CMA på sida motsatt strömförsörjning (sida A). Om den är monterad på sida B, måste CMA kopplas från för att ta bort den yttre strömförsörjningen. 
   > * Innan du tar bort kraften tillhandahåller alltid ta bort enheten. 

   ![Bild av CMA hakparentes installation](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Välj den lämpliga CMA bilaga parentesen för den sida där du vill montera CMA (sida B eller sida A).
   1. Installera CMA bilaga hakparentes med motsvarande sida-A- eller sida B märkning på baksidan bild rail.
   1. Justera hål på hakparentesen med PIN-koder på bild rail. Tryck på hakparentes ner tills det låser på plats. 

4. Installera CMA.

   1. Plats spärr på klientdelen för CMA på den innersta hakparentesen i sammansättningen bild på baksidan av systemet, förrän låset talar med (1). 
   1. Anpassa andra spärr i slutet av den yttersta hakparentesen förrän låset som engagerar (2). 
   1. Ta bort CMA genom att koppla ur båda Lås genom att trycka på knapparna CMA versionen överst i inre och yttre spärr höljen (3).

   ![Illustration av den huvudsakliga CMA installation](media/fxt-install/cma-install-400.png)

   CMA kan roteras bort från systemet för åtkomst och tjänsten. Slutet upphängd lyft CMA bort från enheten om du vill ta bort det (1). När det är unseated från fack svänga CMA bort från systemet (2).

   ![Bild av CMA roteras öppen för tjänsten](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Installera på klientsidan ramen (valfritt)

Det här avsnittet beskrivs hur du installerar och ta bort ramens framsida (fronten) för Azure FXT Edge Filer maskinvaran. 

Installera klient ram: 

1. Leta upp och ta bort ram nyckeln, som finns i RAM-paketet. 
1. Justera ram med framför chassit och infoga PIN-koder på höger sida av ram i hål på nodens höger rack montera flänssmörjning. 
1. Anpassa den vänstra änden av ram på chassit. Tryck på ramen tills knappen på vänster sida klickar på plats.
1. Lås på ramen med nyckeln.

Ta bort front ram: 
1. Lås upp på ramen genom att använda nyckeln ram.
1. Tryck på knappen versionen på vänster sida och dra den vänstra änden av ram från chassit.
1. Koppla loss till höger och ta bort ram.
   
   ![Bild som visar knappen versionen längst till vänster på ram och hur du tar bort den genom att dra ut till vänster](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Nästa steg

När du har packat upp och rackmonterat enheten kan fortsätta med installationen genom att koppla nätverkskablarna och ansluta nätström till Azure FXT Edge-Filer.

> [!div class="nextstepaction"]
> [Ansluta nätverksportar och ange power](fxt-network-power.md)