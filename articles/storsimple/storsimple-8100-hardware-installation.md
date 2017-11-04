---
title: Installera Microsoft Azure StorSimple 8100-enhet | Microsoft Docs
description: Beskriver hur du packa upp, rackmontera och kabelanslut din 8100 StorSimple-enhet innan du distribuerar och konfigurerar programvaran.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 102dffcd73f3d3b9362d7b2853faa060e9c645dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Packa upp, rackmontera, och kabelanslut din 8100 StorSimple-enhet
## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8100-enhet är en enda enhet, rackmonterad enhet. Den här självstudiekursen beskrivs hur du packa upp, rackmonterad och kabel StorSimple 8100-enhet maskinvara innan du konfigurerar och distribuera StorSimple-enhet.

## <a name="unpack-your-storsimple-8100-device"></a>Packa upp din StorSimple 8100-enhet
Följande steg ger tydliga, detaljerade instruktioner om hur du packar upp din StorSimple 8100-lagringsenhet. Den här enheten levereras i en enda ruta.

### <a name="prepare-to-unpack-your-device"></a>Förbereda för att packa upp din enhet
Granska följande information innan du packar upp din enhet.

![Varningsikon](./media/storsimple-safety/IC740879.png)![tunga vikt ikonen](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

1. Kontrollera att du har två personer som är tillgängliga för att hantera vikten för höljet om hanterar manuellt. Ett helt konfigurerade hölje kan väga upp till 32 kg (70 pund).
2. Placeras på en plan, nivå-yta.

Därefter utför följande steg för att packa upp din enhet.

#### <a name="to-unpack-your-device"></a>Att packa upp din enhet
1. Granska rutan och paketering skummet för crushes delar, vattenstämplar skador eller andra uppenbara skador. Om rutan eller paketering är allvarligt skadat kan inte öppna rutan. Kontrollera [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) som hjälper dig att bedöma om enheten är i gott skick.
2. Packa upp rutan. Följande bild visar den uppackade din StorSimple-enhet.
   
     ![Packa upp lagringsenheten](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Packa upp visning av lagringsenheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Packkorg rutan |
   |   2 |Nedre skum |
   |   3 |Enhet |
   |   4 |Övre skum |
   |   5 |Tillbehör rutan |
3. När rutan, se till att du har:
   
   * 1 enhet har hårddiskar.
   * 2 strömkablar
   * 1 övergång Ethernet-kabel
   * 2 seriekonsolen kablar
   * 1 seriell USB konverterare för seriell åtkomst
   * 1 förfalska T10 för
   * 4 QSFP-till-SFP + kort för användning med 10 GbE-nätverkskort
   * 1-rackmonterad kit (2 sida spår med montera maskinvara)
   * Komma igång-dokumentationen
     
     Om du inte tog emot något av objekten i listan ovan, [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).

Nästa steg är att rackmonterade enheten.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rackmonterade StorSimple 8100-enhet
Följ stegen nedan om du vill installera lagringsenheten StorSimple 8100 i en standard 19-tums rack med främre och bakre meddelandena. StorSimple 8100-enhet har en enda primär enhet.

Installationen består av flera steg, som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för att fungera korrekt.
> 
> 

### <a name="prepare-the-site"></a>Förbereda platsen
Enheten måste installeras i ett standard 19-tums rack som har både främre och bakre meddelandena. Använd följande procedur för att förbereda för Rackinstallation av.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Så här förbereder du platsen för Rackinstallation
1. Kontrollera att enheten på ett säkert sätt vilar på en flat stabilt och nivå arbete ytan (eller liknande).
2. Kontrollera att platsen där du vill konfigurera har standard AC-ström från en oberoende källa eller en rack power distributionsenhet (PDU) med en avbrottsfri elkälla (UPS).
3. Kontrollera att en 2U platsen är tillgänglig på rack som du vill montera enheten.

![Varningsikon](./media/storsimple-safety/IC740879.png)![tunga vikt ikonen](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

Kontrollera att du har två personer som är tillgängliga för att hantera vikten om du hantering av Enhetsinställningar manuellt. Ett helt konfigurerade hölje kan väga upp till 32 kg (70 pund).

### <a name="rack-prerequisites"></a>Rack krav
8100-enhet är utformat för installation i en standard 19-tums rack CAB med:

* Minsta djup 27.84 tum från rack post till post.
* Högsta vikt för 32 kg för enheten
* Högsta tillbaka tryck 5 Pascal (0,5 mätare).

### <a name="rack-mounting-rail-kit"></a>Rack montering tåg kit
En uppsättning montera spår har angetts för användning med 19-tums rack kabinettfilen. Spår har testats för att hantera största höljet vikt. Dessa spår kommer också att tillåta installation av flera höljen utan att förlora utrymme i racket.

#### <a name="to-install-the-device-on-the-rails"></a>Så här installerar du enheten i spår
1. Utför det här steget endast om inre spår inte har installerats på enheten. Inre spår installeras vanligtvis på fabriken. Om spår inte har installerats, installerar du tåg vänster och höger tåg bilder till sidorna på höljet chassi. De fäster med sex mått skruvar på varje sida. För att hjälpa med orientering tåg bilder markeras **LH – fram** och **RH – fram**, och end som fästs bakåt från höljet har en avsmalnande end.<br/>
   
    ![Bifoga tåg bilder höljet chassi](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Kopplar inre tåg bilder till sidorna på höljet**
   
    Etikett | Beskrivning
    ----- | -----------
    1     | M 3 x 4 knappen head skruvar
    2     | Chassi bilder

2. Bifoga den yttre vänstra tåg och yttre höger sammansättningar rack CAB lodräta medlemmar. Hakparenteserna markeras **LH**, **RH**, och **den här sidan uppåt** som hjälper dig att rätt orientering.
3. Leta upp PIN-koder för tåg på främre och bakre tåg sammansättningens. Utöka spåret för att passa mellan rack meddelandena och infoga de PIN-koderna i främre och bakre rack post lodräta medlem hål. Se till att sammansättningen tåg är nivå.
4. Använd två av de angivna mått skruvarna för att skydda tåg sammansättningen till racket lodräta medlemmar. Använd en skruv på framsidan och en på Bakåt.
5. Upprepa dessa steg för andra tåg sammansättningen.<br/>
   
     ![Kopplar tåg bilder till rack CAB](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Kopplar yttre tåg sammansättningar till racket**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Minskning skruv |
   |   2 |Ruta hål främre rack post skruv |
   |   3 |Vänster tåg främre plats PIN-koder |
   |   4 |Minskning skruv |
   |   5 |Vänster tåg bakre plats PIN-koder |

### <a name="mounting-the-device-in-the-rack"></a>Montera enheten i racket
Använd rack spår som precis har installerats och utför följande steg för att montera enheten i racket.

#### <a name="to-mount-the-device"></a>Montera enheten
1. Med en assistent lyfter höljet och justera med rack spår.
2. Infoga noggrant enheten i spår och trycker sedan enheten helt i racket cab.<br/>
   
    ![Lägga till enheten i racket](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera enheten i racket**
3. Ta bort vänster och höger främre flänsad caps genom att dra caps ledigt. Flänsad caps Fäst bara till flänsar.
4. Skydda höljet i racket genom att installera en angivna stjärnskruvmejsel head skruv via varje flänsad vänster och höger.
5. Installera flänsad caps genom att trycka ned dem till rätt plats och fästa dem på plats.<br/>
   
     ![Installera flänsad caps](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänsad caps**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Höljet fästanordning skruv |

Nästa steg är att kabelanslut din enhet till ström, nätverk och serieåtkomst.

## <a name="cable-your-storsimple-8100-device"></a>Kabelanslut din 8100 StorSimple-enhet
Följande procedurer beskriver hur du kabelanslut din 8100 StorSimple-enhet till ström, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Krav
Innan du börjar kablage för enheten, behöver du:

* Dina lagringsenhet, helt Uppackad och rackmonterade.
* 2 strömkablar som medföljde din enhet
* Åtkomst till 2 Kraftfördelningsenheter (rekommenderas).
* Nätverkskablarna
* Angivna seriella kablar
* Seriell USB-konverteraren med lämplig drivrutin installeras på din dator (om det behövs)
* Angivna 4 QSFP-till-SFP + kort för användning med 10 GbE-nätverkskort
* [Maskinvara som stöds för 10 GbE-nätverkskort på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Kablar
Enheten innehåller redundant ström och kylning moduler (PCMs). Både PCMs måste installeras och ansluten till olika strömkällor att säkerställa hög tillgänglighet.

Utför följande steg för att kabelanslut din enhet för ström.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablar
Enheten är en aktiv standby-konfiguration: samtidigt, en modul som domänkontrollanten är aktiv och bearbetning av alla disk och nätverk när modulen andra domänkontrollanter är i vänteläge. Om en domänkontrollant inte vänteläge controller aktiveras omedelbart och fortsätter disk- och nätverksfunktioner.

För att stödja den här redundansen redundant domänkontrollant, behöver du kabelanslut din enhet nätverket enligt beskrivningen i följande steg.

#### <a name="to-cable-for-network-connection"></a>Till kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje domänkontrollant: fyra 1 Gbit/s och två 10 Gbit/s Ethernet-portar. Identifiera de olika data-portarna på bakplan av enheten.
   
    ![Bakplan av 8100-enhet](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tillbaka på enheten som visar dataportar**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverkskort |
   |   2,3 |10 GbE-nätverkskort |
   |   6 |Seriella portar |
2. Finns i följande diagram på nätverkskablar. (Minsta nätverkskonfigurationen visas med blå heldragen linje. Ytterligare konfiguration krävs för hög tillgänglighet och prestanda visas med streckade linjer.)

    ![Kabelansluta den 2U för nätverket](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Nätverket kablar för din enhet**

   |Etikett | Beskrivning |
   |----- | ----------- |
   | A    | LAN med Internetåtkomst |
   | B    | Styrenhet 0 |
   | C    | PCM 0 |
   | D    | Kontrollant 1 |
   | E    | PCM 1 |
   | F, G | Värdar |
   | 0-5  | Nätverksgränssnitt |



När kablar enheten kräver den lägsta konfigurationen:

* Minst två nätverkskort som är anslutna på varje domänkontrollant för att komma åt molnet och en för iSCSI. DATA 0 port aktiveras och konfigureras via seriekonsolen för enheten automatiskt. Förutom DATA 0 måste en annan dataport också konfigureras via den klassiska Azure-portalen. I det här fallet ansluta DATA 0 porten till den primära LAN (nätverk med åtkomst till Internet). Andra dataportar kan vara ansluten till SAN/iSCSI-LAN (VLAN)-segmentet i nätverket, beroende på vilken roll som avsett.
* Samma gränssnitt på varje domänkontrollant anslutna till samma nätverk för att säkerställa tillgänglighet om det uppstår redundans domänkontrollant. Till exempel om du vill ansluta DATA 0 och DATA 3 för en av domänkontrollanterna behöver du ansluta motsvarande DATA 0 och DATA 3 på en annan domänkontrollant.

Kom ihåg för hög tillgänglighet och prestanda:

* Konfigurera ett par med nätverksgränssnittet för molnåtkomst (1 GbE) och en annan par för iSCSI (10 GbE rekommenderas) på varje domänkontrollant när det är möjligt.
* När det är möjligt ansluta nätverksgränssnitt från varje domänkontrollant till två olika växlar för att garantera mot en switch-fel. Bilden visar två 10 GbE nätverksgränssnitt, DATA 2 och DATA 3 från varje domänkontrollant som är ansluten till två olika växlar.

Mer information finns i den **nätverksgränssnitt** under den [krav på hög tillgänglighet för din StorSimple-enhet](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP + mottagarna med din 10 GbE-nätverkskort, använder den angivna QSFP-SFP + nätverkskort. Mer information finns på [maskinvara som stöds för 10 GbE-nätverkskort på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Serieport kablage
Utför följande steg för att kabelanslut din serieport.

#### <a name="to-cable-for-serial-connection"></a>Att kabel för seriell anslutning
1. Enheten har en seriell port på varje domänkontrollant som identifieras av en skiftnyckelikonen. Se bilden i den [nätverkskablar](#network-cabling) avsnittet för att hitta de seriella portarna på bakplan av enheten.
2. Identifiera den aktiva styrenheten på din enhet bakplan. En blinkande blå Indikator anger att kontrollanten är aktiv.
3. Använd de angivna seriella kablarna (vid behov, USB-seriell konverteraren för din bärbara dator) och Anslut din dator (med terminalemulering till enheten) eller konsolen till seriell port för den aktiva styrenheten.
4. Installera serial-USB-drivrutiner (medföljer enheten) på datorn.
5. Ställ in seriell anslutning enligt följande: 115 200 baud, 8 databitar, 1 stoppa bitar, ingen paritet och flödeskontroll inställd på None.
6. Kontrollera att anslutningen fungerar genom att trycka på RETUR på konsolen. Menyn för seriekonsolen ska visas.

> [!NOTE]
> **Hantering av lights-Out**: när enheten har installerats i en fjärransluten datacenter eller i en lokal dator med begränsad åtkomst, kontrollera att de seriella anslutningarna till både domänkontrollanter alltid är ansluten till en seriekonsolen växel eller liknande utrustning. Detta tillåter fjärrstyrning för out-of-band och stöd för åtgärder om det finns nätverksstörningar eller oväntade fel.
> 
> 

Enheten är nu kabelansluten till ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran och distribuera din enhet.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

