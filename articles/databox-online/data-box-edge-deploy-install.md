---
title: Självstudie om du vill installera - packa upp, racka, kabel fysiska Azure Data Box Edge-enhet | Microsoft Docs
description: Den andra självstudien om att installera Azure Data Box Edge omfattar att packa upp, racka och kabelansluta den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 7062e9d829242a62dfa4f661c21ce6c56b0db69c
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924528"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Självstudier: Installera Azure Data Box Edge

Den här självstudien beskriver hur du installerar en fysisk Data Box Edge-enhet. Installationsproceduren omfattar uppackning, rackmontering och kabelanslutning av enheten. 

Det kan ta cirka två timmar att slutföra installationen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

## <a name="prerequisites"></a>Nödvändiga komponenter

De nödvändiga komponenterna för att installera en fysisk enhet är följande:

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar ska du kontrollera att:

* Du har slutfört alla steg i [förbereda för distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).
    * Du har skapat en Data Box Edge-resurs för att distribuera enheten.
    * Du har genererat aktiveringsnyckeln för att aktivera din enhet med Data Box Edge-resursen.

 
### <a name="for-the-data-box-edge-physical-device"></a>För den fysiska Data Box Edge-enheten

Innan du distribuerar en enhet:

- Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta.
- Kontrollera att den plats där du ska konfigurera har:
    - Vanlig nätström från en oberoende källa

        ELLER
    - En strömfördelare (PDU) för rack med en avbrottsfri strömkälla (UPS)
    - En tillgänglig 1U-plats på det rack där du ska montera enheten

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret

Innan du börjar:

- Granska nätverkskraven för att distribuera en Data Box Edge och konfigurera datacenternätverket enligt kraven. Mer information finns i [Nätverkskrav för Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Se till att den minsta Internetbandbredden är 20 Mbps för att enheten ska fungera optimalt.


## <a name="unpack-the-device"></a>Packa upp enheten

Den här enheten levereras i en enda låda. Slutför följande steg för att packa upp enheten. 

1. Placera lådan på en plan, jämn yta.
2. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna den. Kontakta Microsoft Support för att få hjälp att bedöma om enheten är i gott skick.
3. Packa upp lådan. När du har packat upp lådan kontrollerar du att det finns:
    - En Edge-enhet med ett enskilt hölje
    - Två strömkablar
    - En rail kit sammansättning
    - En säkerhet, miljö och föreskrifter broschyr

Om något av ovanstående saknas kontaktar du supporten för Data Box Edge. Nästa steg är att rackmontera enheten.


## <a name="rack-the-device"></a>Rackmontera enheten

Enheten måste installeras i ett 19-tums standardrack. Använd följande procedur för att rack montera din enhet i en standard 19-tums ställning.

> [!IMPORTANT]
> Data Box Edge-enheter måste rackmonteras för att fungera korrekt.


### <a name="prerequisites"></a>Nödvändiga komponenter

- Innan du kan läsa anvisningarna säkerhet i din säkerhet, miljö och föreskrifter broschyr. Den här häfte levererades med enheten.
- Används för att börja installeras rails det tilldelade utrymmet som är närmast längst ned på höljet rack.
- För tooled rail montering konfiguration:
    -  Du måste ange åtta skruvar: #10-32, #12-24, #M5 eller #M6. Skruvarna head diameter måste vara mindre än 10 mm (0,4 ”).
    -  Du behöver en fast spets för.

### <a name="identify-the-rail-kit-contents"></a>Identifiera rail kit innehållet

Leta upp komponenterna för att installera rail kit sammansättningen:
1. Dell ReadyRails av två A7-II som glidande rail sammansättningar
2. Två hook och slinga remmar

![Identifiera rail kit innehåll](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Installera och ta bort verktyg rails (kvadratisk hål eller runda hål rack)

> [!TIP]
> Det här alternativet är verktyg eftersom det inte kräver verktyg för att installera och ta bort rails i icke gängade kvadraten eller avrunda hål i serverrack.

1. Placera vänster och höger rail slutet delar märkta **FRONT** inför inåt och förstå varje slutpunkt del till enhet i hål på de lodräta racklister framsida.
2. Justera varje slutpunkt del i de nedre och övre hål av de önskade U adressutrymmena.
3. Engagera spåret backend-servern tills den fullständigt platser på lodrät rack flänsens och låset klickar på plats. Upprepa dessa steg om du vill placera och enhet klientdelen del på flänsens lodrät rack.
4. Ta bort rails, hämta knappen spärr versionen på slutet del mittpunkten och ta bort varje rail.

![Installera och ta bort verktyg rails](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Installera och ta bort tooled rails (listvy hål rack)

> [!TIP]
> Det här alternativet är tooled eftersom den kräver ett verktyg (_en fast spets för_) att installera och ta bort rails i flertrådade runda hål i serverrack.

1. Ta bort de PIN-koderna från främre och bakre montera hakparenteser med hjälp av en fast spets för.
2. Hämta och rotera rail spärr delprodukter om du vill ta bort dem från montering hakparenteser.
3. Ansluta till vänster och höger montera rails till front lodrät racklister med hjälp av två par skruvar.
4. Dra tillbaka vänster och höger hakparentes vidarebefordra mot de bakre lodrät racklister och bifoga dem med hjälp av två par skruvar.

![Installera och ta bort tooled rails](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Installera i en rack

1. Hämta inre bild rails utanför racket tills de lås på plats.
2. Leta upp bakre rail standoff på båda sidor av systemet och sänka dem till bakre J-fack på bild-sammansättningar. Rotera systemet nedåt tills alla rail standoffs har satts i J-platserna.
3. Skicka systemet inåt tills Lås steg klickar du på på plats.
4. Tryck på bild-versionen Lås knappar på både rails och bild systemet i racket.

![Installera system i ett rack](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

1. Leta upp Lås reglage på sidorna av de inre rails.
2. Lås upp varje spak genom att rotera upp till dess version position.
3. Rapportelementen sidor av systemet ordentligt och dra den framåt tills rail standoffs är längst fram av J-platserna. Lyft systemet upp och från racket och placera den på en nivå för angrepp.

![Ta bort system från racket](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Engagera och frigöra slam spärr

> [!NOTE]
> För system som inte är utrustade med slam lås, skydda systemet med skruvar, enligt beskrivningen i steg 3 i den här proceduren.

1. Klientdelen som riktas mot, leta upp slam spärr på endera sidan av systemet.
2. Lås engagera automatiskt när systemet pushas till racket och släpps genom att dra på Lås.
3. Om du vill skydda system för leverans i racket eller för andra instabil miljöer, leta upp hårda montering skruven under varje Lås och öka varje skruv med ett #2 stjärnskruvmejsel.

![Engagera och frigöra slam spärr](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>Dirigera kablarna

> [!NOTE]
>  Om du inte har ordning valfritt kabel Management Arm (CMA) använder två hook och loopa remmar i rail kit att dirigera kablar på baksidan av systemet.

1. Leta upp de yttre CMA hakparenteserna båda racklister inre sidor.
2. Paketera kablarna försiktigt, att de Rensa av system-anslutningarna till vänster och höger.
3. Tråd hook och slinga remmar via fack på yttre CMA hakparenteserna på båda sidor av systemet för att skydda en kabel-paket.


![Dirigera kablarna](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>Kabelansluta enheten

Följande procedurer beskriver hur du kabelansluter Edge-enheten för strömförsörjning och nätverk.

Innan du börjar kabelansluta enheten behöver du följande:

- Den fysiska Edge-enheten uppackad och rackmonterad.
- Två strömkablar. 
- Minst en 1-GbE RJ-45-nätverkskabel för att ansluta till hanteringsgränssnittet. Det finns två 1-GbE-nätverksgränssnitt på enheten, ett för hantering och ett för data.
- En 25-GbE SFP+-kopparkabel för varje datanätverksgränssnitt som ska konfigureras. Minst ett datanätverksgränssnitten PORT 2, PORT 3, PORT 4, PORT 5 eller PORT 6 måste vara anslutet till Internet (med anslutning till Azure).  
- Åtkomst till två strömfördelare (rekommenderas).

> [!NOTE]
> - Om du ansluter bara ett nätverksgränssnitt för data, rekommenderar vi att du använder ett 25/10 GbE-nätverksgränssnitt som PORT 3, PORT 4, PORT 5 eller 6 PORT för att skicka data till Azure. 
> - För att få bästa prestanda och hantera stora datavolymer bör du ansluta alla dataportar.
> - Edge-enheten bör anslutas till datacenternätverket så att den kan mata in data från datakällservrar.

Edge-enheten 8 NVMe SSD-enheter. Frontpanel har även statuslampor och strömknappar. Enheten innehåller redundanta nätaggregat (PSU) i den bakre delen. Enheten har sex nätverksgränssnitt:

- Två 1 Gbit/s-gränssnitt
- Fyra 25 Gbit/s-gränssnitt kan också fungera som 10 Gbit/s-gränssnitt.
- En hanteringsstyrenhet för baskort (BMC). 

Identifiera de olika portarna på backsidan av enheten.
 
  ![Baksidan på en kabelansluten enhet](./media/data-box-edge-deploy-install/backplane-cabled.png)

Enheten har två nätverkskort som motsvarar de 6 portarna: 

 - QLogic FastLinQ 41264
 - QLogic FastLinQ 41262

En fullständig lista över stöds kablar, växlar och mottagarna för dessa nätverkskort finns i [Cavium FastlinQ 41000 serien samverkan matrisen](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Anslut strömkablarna till vart och ett av nätaggregaten i höljet. För att säkerställa hög tillgänglighet installerar och ansluter du båda nätaggregaten till olika strömkällor.

2. Anslut strömkablarna till rackets strömfördelare (PDU). Se till att de två strömfördelarna använder separata strömkällor.

3. Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.

4. Anslut en eller flera av PORT 2, PORT 3, PORT 4, PORT 5 eller 6 PORT till datacenternätverket/Internet. Om du ansluter PORT 2 använder du RJ-45-nätverkskabeln. Använd kablar SFP +-kopparkabel för 10/25 GbE-nätverksgränssnitt.  

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar enheten.

> [!div class="nextstepaction"]
> [Ansluta och konfigurera Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)


