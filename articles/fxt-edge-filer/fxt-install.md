---
title: 'Självstudiekurs: Installera en fysisk Azure FXT Edge Filer-enhet'
description: Så här packar du upp, rackar och kabelredar den fysiska enhetskomponenten i Microsoft Azure FXT Edge Filer hybridlagringscache
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551037"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Självstudiekurs: Installera Azure FXT Edge Filer 

Den här självstudien beskriver hur du installerar en maskinvarunod för Azure FXT Edge Filer hybridlagringscache. Du måste installera minst tre maskinvarunoder för att skapa ett Azure FXT Edge Filer-kluster.

Installationsproceduren innebär uppackning och rackmontering av enheten och fastsättning av kabelhanteringsarmen (CMA) och den främre ramen. En separat självstudiekurs förklarar att ansluta nätverkskablar och ansluta ström. 

Det tar ungefär en timme att installera en Azure FXT Edge Filer-nod. 

Den här självstudien innehåller följande installationssteg: 

> [!div class="checklist"]
> * Packa upp enheten
> * Montera enheten i ett rack
> * Installera den främre ramen (tillval)

## <a name="installation-prerequisites"></a>Installationskrav 

Innan du börjar kontrollerar du att det datacenter och det rack du ska använda har följande funktioner:

* En tillgänglig 1U-kortplats på racket där du tänker montera enheten.
* Ac-strömförsörjning och kylsystem som uppfyller behoven hos Azure FXT Edge Filer. (Läs [effekt- och termiska specifikationer](fxt-specs.md#power-and-thermal-specifications) för hjälp med planering och storlek på installationen.)  

  > [!NOTE] 
  > Använd kraftfördelningsenheter på två olika grenkretsar för att dra full nytta av de två redundanta nätaggregaten när du ansluter nätaggregat. Läs [Anslut strömkablar](fxt-network-power.md#connect-power-cables) för mer information.  

## <a name="unpack-the-hardware-node"></a>Packa upp maskinvarunoden 

Varje Azure FXT Edge Filer-nod levereras i en enda ruta. Slutför de här stegen för att packa upp en enhet.

1. Placera lådan på en plan, jämn yta.

2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna den. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.

3. Packa upp lådan. Kontrollera att följande innehåller följande:
   * En FXT-enhet med en enda kapsling
   * Två strömkablar
   * En främre ram och nyckel
   * En montering av skensatsen
   * En kabelhanteringsarm (CMA)
   * CMA installationsinstruktioner häfte
   * Häfte för rackinstallationsanvisningar
   * Häfte för säkerhet, miljö och regulatorisk information

Om du inte har fått alla artiklar i listan kontaktar du enhetsleverantören för support. 

Se till att enheten har haft tillräckligt med tid för att nå samma temperatur som rummet innan du installerar den eller slå på den. Om du märker kondens på någon del av enheten, vänta minst 24 timmar innan du installerar.

Nästa steg är att rackmontera enheten.

## <a name="rack-the-device"></a>Rackmontera enheten

Azure FXT Edge Filer-enheten måste installeras i ett 19-tums standardrack. 

Azure FXT Edge Filer hybridlagringscache består av tre eller fler Azure FXT Edge Filer-enheter. Upprepa rackinstallationsstegen för varje enhet som ingår i systemet. 

### <a name="rack-install-prerequisites"></a>Förutsättningar för rackinstallation

* Innan du börjar kan du läsa säkerhetsinstruktionerna i häftet Säkerhets-, miljö- och regulatorisk information som levererades med enheten.

  > [!NOTE]
  > Använd alltid två personer när du lyfter noden, även när du installerar den i ett rack eller tar bort den från racket. 

* Identifiera vilken typ av järnvägsinstallation som används med ditt utrustningsställ. 
  * För fyrkantiga eller runda hål snap-in rack, följ verktygslösa skenan instruktioner.
  * För gängade hålställ, följ de verktygsstängsanvisningarna. 
  
    För den verktygssyriga monteringskonfigurationen måste du leverera åtta skruvar, typ 10-32, 12-24, M5 eller M6. Skruvarnas huvuddiameter skall vara mindre än 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identifiera innehållet i skensatsen

Leta upp komponenterna för installation av monteringen av skensatsen:

1. Två A7 Dell ReadyRails II glidande skenor (1)
1. Två krok- och öglare (2)

![Numrerad ritning av innehållet i järnvägssatsen](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Skena - verktygslösa skenor (Kvadratiska hål eller runda hålställ)

För rack med snap-in kvadratiska eller runda hål, följ denna procedur för att montera och installera skenorna. 

1. Placera vänster och höger skena änddelar **märkta FRONT** vänd inåt. Placera varje ändstycke så att den sitter i hålen på framsidan av de vertikala rackflänsarna. (1)

2. Justera varje ändstycke i botten och de övre hålen i rack i det utrymme du vill montera den. (2)

3. Koppla in rälsens bakre ände tills den sitter helt på den vertikala rackflänsen och spärren klickar på plats. Upprepa dessa steg för att placera och placera frontdelen på den vertikala rackflänsen. (3)

> [!TIP]
> För att ta bort skenorna, dra i spärrens frigöringsknapp på ändstyckets mittpunkt (4) och ta bort varje skena.

![Diagram över att installera och ta bort verktygslösa skenor, med steg numrerade](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Skenad - verktygsskenor (Gängade hålställ)

För rack med gängade hål, följ denna procedur för att montera och installera skenorna.

1. Ta bort stiften från de främre och bakre monteringsfästena med en plattskruvmejsel. (1)
1. Dra och vrid skenspärrens delenheter för att ta bort dem från monteringsfästena. (2)
1. Fäst vänster och höger monteringsskenor på de främre vertikala rackflänsarna med två par skruvar. (3)
1. Skjut vänster och höger ryggfästen framåt mot de bakre vertikala rackflänsarna och fäst dem med två par skruvar. (4)

![Diagram över att installera och ta bort verktygsskenor, med steg numrerade](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Installera systemet i racket

Följ dessa steg för att montera Azure FXT Edge Filer-enheten i racket.

1. Dra ut de inre glidskenorna ur racket tills de låss på plats. (1)
1. Placera den bakre skenans dödläge på varje sida av enheten och sänk dem i de bakre J-spåren på glidenheterna. (2) 
1. Vrid enheten nedåt tills alla skenor är på plats i J-spåren. (3)
1. Tryck in enheten mot apparaten tills låsspakarna klickar på plats.
1. Tryck på låsknapparna för glidkoppling på båda skenorna (4) och skjut in enheten i racket.

![Installera systemet i ett rackdiagram med steg numrerade](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

Följ den här proceduren om du vill ta bort enheten från racket. 

1. Placera låsspakarna på sidorna av de inre skenorna (1).
2. Lås upp varje spak genom att vrida upp den till dess frigöringsläge (2).
3. Ta tag i sidorna av systemet ordentligt och dra det framåt tills skenan standoffs är på framsidan av J-slots. Lyft upp systemet och bort från racket och placera det på en plan yta (3).

![Illustration av att ta bort ett system från racket, med steg numrerade](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Koppla in slamspärren

1. Vänd framåt, leta upp slamspärren (1) på vardera sidan av systemet.
2. Spärrarna aktiveras automatiskt när systemet trycks in i racket. 

För att lossa spärrarna när du tar bort systemet, dra upp dem (2).

Hard-mount skruvar som tillval finns för att säkra systemet till racket för transport eller i andra instabila miljöer. Hitta skruven under varje spärr och dra åt dem med en #2 Phillips skruvmejsel (3).

![Numrerad illustration av att aktivera och släppa slamspärren](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Installera kabelhanteringsarmen 

En kabelhanteringsarm (CMA) som tillval är försedd med FXT Edge Filer. Tryckta instruktioner för att installera det finns i förpackningen. 

1. Packa upp och identifiera komponenterna i kabelhanteringsarmsatsen:
   * CMA-fack (1)
   * CMA (2)
   * Nylon kabel slips wraps (3)
   * CMA fästen (4)
   * Statusindikatorkabel (5) 

   > [!TIP] 
   > För att säkra CMA för transport i racket, slinga slipsen wraps runt både korgar och bricka och cinch dem ordentligt. Att säkra CMA på detta sätt kommer också att skydda ditt system i instabila miljöer.

   ![Illustration av CMA delar](media/fxt-install/cma-kit-400.png)

2. Installera CMA-facket.

   CMA-facket ger stöd och fungerar som hållare för CMA. 

   1. Rikta in och koppla in varje sida av facket med mottagarfästena på skenornas innerkanter. 
   1. Tryck facket framåt tills det klickar på plats. (1)
   1. För att ta bort facket, tryck spärrknapparna mot mitten och dra ut facket ur mottagarfästena (2).

   ![Illustration av CMA-fackinstallation](media/fxt-install/cma-tray-install-400.png)

3. Installera CMA-fästen. 

   > [!NOTE]
   >
   > * Du kan fästa CMA till antingen höger eller vänster monteringsskena, beroende på hur du tänker dra kablar från systemet. 
   > * För enkelhetens skull, montera CMA på sidan mittemot nätaggregat (sida A). Om cma är monterat på sidan B måste den kopplas bort för att ta bort den yttre strömförsörjningen. 
   > * Ta alltid bort facket innan du tar bort nätaggregatet. 

   ![Illustration av CMA-fästesinstallation](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Välj lämpligt CMA-fästfäste för den sida där du vill montera CMA (sida B eller sida A).
   1. Montera CMA-fästet med motsvarande sida A- eller sido B-markering på baksidan av glidskenan.
   1. Rikta in hålen på fästet med stiften på glidskenan. Tryck ner fästet tills det låses på plats. 

4. Installera CMA.

   1. På baksidan av systemet, montera spärren på den främre änden av CMA på den innersta fästet på glidenheten tills spärren griper in (1). 
   1. Montera den andra spärren på änden av det yttersta fästet tills spärren griper in (2). 
   1. För att ta bort CMA, lossa båda spärrarna genom att trycka på CMA-frigöringsknapparna högst upp på de inre och yttre låshusen (3).

   ![Illustration av huvudsaklig CMA installation](media/fxt-install/cma-install-400.png)

   CMA kan roteras bort från systemet för åtkomst och service. Lyft cma:en från facket i den gångjärnsförsedda änden för att ta bort den (1). När den har avsnräckts från facket, sväng CMA bort från systemet (2).

   ![Illustration av CMA roteras öppen för service](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Installera den främre ramen (tillval)

I det här avsnittet beskrivs hur du installerar och tar bort den främre ramen (frontplate) för Azure FXT Edge Filer-maskinvaran. 

Så här installerar du den främre ramen: 

1. Leta upp och ta bort bezelnyckeln, som finns i bezelpaketet. 
1. Rikta in ramen mot chassits framsida och sätt in stiften på ramens högra sida i hålen på nodens högra sidoräcke. 
1. Montera den vänstra änden av ramen på chassit. Tryck på ramen tills knappen på vänster sida klickar på plats.
1. Lås ramen med nyckeln.

Så här tar du bort den främre ramen: 
1. Lås upp ramen med hjälp av bezel-tangenten.
1. Tryck på frigöringsknappen på vänster sida och dra den vänstra änden av ramen bort från chassit.
1. Haka av den högra änden och ta bort ramen.
   
   ![Bild som visar frigöringsknappen till vänster om ramen och hur man tar bort den genom att dra utåt från vänster sida](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Nästa steg

När du har packat upp och rackat enheten fortsätter du installationen genom att ansluta nätverkskablar och ansluta växelström till Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Kabel nätverksportar och strömförsörjning](fxt-network-power.md)