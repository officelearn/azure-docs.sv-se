---
title: Självstudiekurs för installation – Packa upp, rack, kabel Azure Data Box Edge fysisk enhet | Microsoft-dokument
description: Den andra självstudien om att installera Azure Data Box Edge omfattar att packa upp, racka och kabelansluta den fysiska enheten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263956"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Självstudiekurs: Installera Azure Data Box Edge

Den här självstudien beskriver hur du installerar en fysisk Data Box Edge-enhet. Installationsproceduren omfattar uppackning, rackmontering och kabelanslutning av enheten. 

Det kan ta cirka två timmar att slutföra installationen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

## <a name="prerequisites"></a>Krav

De nödvändiga komponenterna för att installera en fysisk enhet är följande:

### <a name="for-the-data-box-edge-resource"></a>För Data Box Edge-resursen

Innan du börjar bör du kontrollera att:

* Du har slutfört alla steg i [Förbereda för att distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md).
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
    - En enda höljesdatabox edge-enhet
    - Två strömkablar
    - En montering av skensatsen
    - Ett häfte om information om säkerhet, miljö och reglerande

Om något av ovanstående saknas kontaktar du supporten för Data Box Edge. Nästa steg är att rackmontera enheten.


## <a name="rack-the-device"></a>Rackmontera enheten

Enheten måste installeras i ett 19-tums standardrack. Använd följande procedur för att rackmontera enheten på ett 19-tums standardställ.

> [!IMPORTANT]
> Data Box Edge-enheter måste rackmonteras för att fungera korrekt.


### <a name="prerequisites"></a>Krav

- Innan du börjar kan du läsa säkerhetsinstruktionerna i häftet Säkerhet, Miljö och Regulatorisk information. Det här häftet levererades med enheten.
- Börja installera skenorna i det tilldelade utrymmet som ligger närmast botten av rackhöljet.
- För den verktygsseda skenmonteringskonfigurationen:
    -  Du måste leverera åtta skruvar: #10-32, #12-24, #M5 eller #M6. Skruvarnas huvuddiameter skall vara mindre än 10 mm (0,4").
    -  Du behöver en plattskruvmejsel.

### <a name="identify-the-rail-kit-contents"></a>Identifiera innehållet i skensatsen

Leta upp komponenterna för installation av monteringen av skensatsen:
1. Två A7 Dell ReadyRails II glidande skenor
2. Två krok- och öglare

    ![Identifiera innehållet i järnvägssatsen](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Installera och ta bort verktygslösa skenor (fyrkantiga hål eller runda hålställ)

> [!TIP]
> Detta alternativ är verktygslös eftersom det inte kräver verktyg för att installera och ta bort skenorna i de otrådade fyrkantiga eller runda hålen i racken.

1. Placera vänster och höger skena änddelar **märkta FRÄMRE** vänd inåt och rikta varje ändstycke till säte i hålen på framsidan av de vertikala rackflänsarna.
2. Rikta in varje ändstycke i botten och de övre hålen i de önskade U-utrymmena.
3. Koppla in rälsens bakre ände tills den sitter helt på den vertikala rackflänsen och spärren klickar på plats. Upprepa dessa steg för att placera och placera frontdelen på den vertikala rackflänsen.
4. För att ta bort skenorna, dra i spärrens frigöringsknapp på ändstyckets mittpunkt och ta bort varje skena.

    ![Installera och ta bort verktygslösa skenor](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Installera och ta bort verktygsskenor (Gängade hålställ)

> [!TIP]
> Detta alternativ är verktyg eftersom det kräver ett verktyg _(en platt-tippade skruvmejsel)_ för att installera och ta bort skenorna i gängade runda hål i racken.

1. Ta bort stiften från de främre och bakre monteringsfästena med en skruvmejsel med platt spets.
2. Dra och vrid skenspärrens delenheter för att ta bort dem från monteringsfästena.
3. Fäst vänster och höger monteringsskenor på de främre vertikala rackflänsarna med två par skruvar.
4. Skjut vänster och höger ryggfästen framåt mot de bakre vertikala rackflänsarna och fäst dem med två par skruvar.

    ![Installera och ta bort verktygsskenor](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Installera systemet i ett rack

1. Dra ut de inre glidskenorna ur racket tills de låss på plats.
2. Placera den bakre skenans dödläge på varje sida av systemet och sänk ner dem i de bakre J-spåren på glidenheterna. Rotera systemet nedåt tills alla skenor är på plats i J-spåren.
3. Tryck in systemet mot tills låsspakarna klickar på plats.
4. Tryck på glidlåsknapparna på båda skenorna och skjut in systemet i racket.

    ![Installera systemet i ett rack](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Ta bort systemet från racket

1. Placera låsspakarna på sidorna av de inre skenorna.
2. Lås upp varje spak genom att rotera den upp till dess frigöringsposition.
3. Ta tag i sidorna av systemet ordentligt och dra det framåt tills skenan standoffs är på framsidan av J-slots. Lyft upp systemet och bort från racket och placera det på en plan yta.

    ![Ta bort systemet från racket](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Koppla in och släpp slamspärren

> [!NOTE]
> För system som inte är utrustade med slamlås, säkra systemet med hjälp av skruvar, enligt beskrivningen i steg 3 i detta förfarande.

1. Vänd framåt, leta upp slamspärren på vardera sidan av systemet.
2. Spärrarna aktiveras automatiskt när systemet trycks in i racket och frigörs genom att dra upp spärrarna.
3. För att säkra systemet för transport i racket eller för andra instabila miljöer, placera den hårda skruven under varje spärr och dra åt varje skruv med en #2 Phillips skruvmejsel.

    ![Engagera och släpp slam spärren](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Kabelansluta enheten

Dra kablarna och kabel-tv-apparaten. Följande procedurer förklarar hur du kabel din Data Box Edge-enhet för ström och nätverk.

Innan du börjar kabelansluta enheten behöver du följande:

- Din fysiska enhet för DataBox Edge, uppackad och rackmonterad.
- Två strömkablar.
- Minst en 1-GbE RJ-45-nätverkskabel för att ansluta till hanteringsgränssnittet. Det finns två 1-GbE-nätverksgränssnitt på enheten, ett för hantering och ett för data.
- En 25-GbE SFP+-kopparkabel för varje datanätverksgränssnitt som ska konfigureras. Minst ett datanätverksgränssnitten PORT 2, PORT 3, PORT 4, PORT 5 eller PORT 6 måste vara anslutet till Internet (med anslutning till Azure).  
- Åtkomst till två strömfördelare (rekommenderas).

> [!NOTE]
> - Om du bara ansluter ett datanätverksgränssnitt rekommenderar vi att du använder ett nätverksgränssnitt på 25/10 GBE, till exempel PORT 3, PORT 4, PORT 5 eller PORT 6 för att skicka data till Azure. 
> - För att få bästa prestanda och hantera stora datavolymer bör du ansluta alla dataportar.
> - DataBox Edge-enheten bör vara ansluten till datacenternätverket så att den kan mata in data från datakällservrar.

På din Data Box Edge-enhet:

- Frontpanelen har hårddiskar och en strömbrytare.

    - Det finns 10 diskplatser på enhetens framsida.
    - Plats 0 har en SATA-enhet på 240 GB som används som operativsystemdisk. Plats 1 är tom och fack 2 till 9 är NVMe SSD som används som datadiskar.
- Det bakre planet innehåller redundanta nätaggregat.
- Det bakre planet har sex nätverksgränssnitt:

    - Två gränssnitt med 1 Gbit/s.
    - Fyra 25-Gbps-gränssnitt som också kan fungera som 10 Gbit/s-gränssnitt.
    - En styrenhet för baseboardhantering (BMC).

- Det bakre planet har två nätverkskort som motsvarar de 6 portarna:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

En fullständig lista över kablar, switchar och sändtagare som stöds för dessa nätverkskort finns i [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Utför följande steg för att kabelansluta enheten för strömförsörjning och nätverk.

1. Identifiera de olika portarna på enhetens bakre plan.

    ![Bakre plan på en kabelanordning](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Leta reda på diskplatserna och strömbrytaren på enhetens framsida.

    ![Främre plan på en enhet](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Anslut strömkablarna till vart och ett av nätaggregaten i höljet. För att säkerställa hög tillgänglighet installerar och ansluter du båda nätaggregaten till olika strömkällor.
4. Anslut strömkablarna till rackets strömfördelare (PDU). Se till att de två strömfördelarna använder separata strömkällor.
5. Tryck på strömbrytaren för att slå på enheten.
6. Anslut PORT 1 på nätverksgränssnittet med 1 GbE till den dator som används för att konfigurera den fysiska enheten. PORT 1 är det dedikerade hanteringsgränssnittet.
7. Anslut en eller flera av PORT 2, PORT 3, PORT 4, PORT 5 eller 6 PORT till datacenternätverket/Internet.

    - Om du ansluter PORT 2 använder du RJ-45-nätverkskabeln.
    - För 10/25-GbE-nätverksgränssnitten använder du SFP+-kopparkablarna.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om Data Box Edge-ämnen som att:

> [!div class="checklist"]
> * Packa upp enheten
> * Rackmontera enheten
> * Kabelansluta enheten

Gå vidare till nästa självstudie och lär dig hur du ansluter, konfigurerar och aktiverar enheten.

> [!div class="nextstepaction"]
> [Ansluta och konfigurera Data Box Edge](./data-box-edge-deploy-connect-setup-activate.md)
