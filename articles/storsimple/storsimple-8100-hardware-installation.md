---
title: Installera Microsoft Azure StorSimple 8100-enhet
description: Beskriver hur du packar upp, rackfäste och kabel din StorSimple 8100-enhet innan du distribuerar och konfigurerar programvaran.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267591"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Packa upp, rackmontera och kabel din StorSimple 8100-enhet

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8100 är en enda hölje, rackmonterad enhet. I den här självstudien beskrivs hur du packar upp, rackmonterar och kabelrubjerar StorSimple 8100-enhetens maskinvara innan du konfigurerar och distribuerar StorSimple-enheten.

## <a name="unpack-your-storsimple-8100-device"></a>Packa upp din StorSimple 8100-enhet
Följande steg innehåller tydliga, detaljerade instruktioner om hur du packar upp lagringsenheten För StorSimple 8100. Den här enheten levereras i en enda låda.

### <a name="prepare-to-unpack-your-device"></a>Förbered att packa upp enheten
Läs följande information innan du packar upp enheten.

![Varning](./media/storsimple-safety/IC740879.png)![Icon tung](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) vikt ikon **VARNING!**

1. Se till att du har två personer tillgängliga för att hantera vikten på höljet om du hanterar den manuellt. En fullt konfigurerad kapsling kan väga upp till 32 kg.
2. Placera lådan på en plan, jämn yta.

Slutför sedan följande steg för att packa upp enheten.

#### <a name="to-unpack-your-device"></a>Så här packar du upp enheten
1. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att hjälpa dig att bedöma om enheten är i gott skick.
2. Packa upp lådan. Följande bild visar den uppackade vyn för StorSimple-enheten.
   
     ![Packa upp lagringsenheten](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **Uppackad vy av lagringsenheten**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Packbox |
   |   2 |Botten skum |
   |   3 |Enhet |
   |   4 |Övre skum |
   |   5 |Tillbehörslåda |
3. När du har packat upp lådan kontrollerar du att det finns:
   
   * 1 enhet med en kapsling
   * 2 nätsladdar
   * 1 korsad Ethernet-kabel
   * 2 seriella konsolkablar
   * 1 serie-USB-omvandlare för seriell åtkomst
   * 1 manipuleringssäker T10 skruvmejsel
   * 4 QSFP-till-SFP+-kort för användning med 10 GbE-nätverksgränssnitt
   * 1 rackmonterad sats (2 sidoskenor med monteringsbeslag)
   * Komma igång-dokumentation
     
     Om du inte har fått något av objekten i listan ovan [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).

Nästa steg är att rackmontera enheten.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rackmontera Din StorSimple 8100-enhet
Följ nästa steg för att installera lagringsenheten StorSimple 8100 i ett 19-tums standardställ med främre och bakre stolpar . StorSimple 8100-enheten har en enda primär kapsling.

Installationen består av flera steg, som var och en diskuteras i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för korrekt drift.
> 
> 

### <a name="prepare-the-site"></a>Förbered webbplatsen
Enheten måste installeras i ett standard rack på 19 tum som har både främre och bakre stolpar. Använd följande procedur för att förbereda för rackinstallation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Så här förbereder du platsen för rackinstallation
1. Kontrollera att enheten på ett säkert sätt vilar på en plan, stabil och jämn arbetsyta (eller liknande).
2. Kontrollera att platsen där du tänker ställa in har standardström från en oberoende källa eller en rackkraftfördelningsenhet (PDU) med en avbrottsfri strömförsörjning (UPS).
3. Se till att en 2U-kortplats finns tillgänglig på det rack där du tänker montera enheten.

![Varning](./media/storsimple-safety/IC740879.png)![Icon tung](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) vikt ikon **VARNING!**

Se till att du har två personer tillgängliga för att hantera vikten om du hanterar enhetskonfigurationen manuellt. En fullt konfigurerad kapsling kan väga upp till 32 kg.

### <a name="rack-prerequisites"></a>Rack förutsättningar
8100-höljet är utformat för installation i ett 19-tums rackskåp med standard 19-tums rackskåp med:

* Minsta djup på 27,84 inches från rack post till post.
* Maximal vikt på 32 kg för enheten
* Maximalt mottryck på 5 Pascal (0,5 mm vattenmätare).

### <a name="rack-mounting-rail-kit"></a>Rackmonteringsskenasats
En uppsättning monteringsskenor finns för användning med 19-tums rackskåp. Skenorna har testats för att hantera den maximala kapslingsvikten. Dessa skenor kommer också att möjliggöra installation av flera kapslingar utan förlust av utrymme i racket.

#### <a name="to-install-the-device-on-the-rails"></a>Så här installerar du enheten på rälsen
1. Utför bara det här steget om inre skenor inte är installerade på enheten. Vanligtvis installeras de inre skenorna på fabriken. Om skenor inte är installerade, installera sedan vänster-skena och höger-skena diabilder på sidorna av kapsling chassit. De fäster med sex metriska skruvar på varje sida. För att hjälpa till med orientering, är skenrutschbanorna **märkta LH - Front** och **RH - Front**, och den ände som är fäst mot baksidan av inneslutningen har en avsmalnande ände.<br/>
   
    ![Fästa rälsglas till kapslingschassi](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Fästa innerskenor glider på sidorna av inneslutningen**
   
    Label (Etikett) | Beskrivning
    ----- | -----------
    1     | M 3x4 knapphuvud skruvar
    2     | Chassiglas

2. Fäst den yttre vänstra skenan och de yttre högra skenenheterna på rackskåpets vertikala enheter. Hakparenteserna är märkta **LH,** **RH**och **Den här sidan uppåt** för att guida dig genom rätt orientering.
3. Leta upp spårstiften på framsidan och baksidan av spårenheten. Förläng skenan för att passa mellan rackstolparna och för in stiften i de främre och bakre rackstolpens vertikala medlemshål. Se till att skenenheten är jämn.
4. Använd två av de medföljande metriska skruvarna för att säkra skenenheten till rackvertredarna. Använd en skruv på framsidan och en på baksidan.
5. Upprepa dessa steg för den andra rälsenheten.<br/>
   
     ![Fästa rälsrutschbanor på rackskåp](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fästa yttre skenenheter till racket**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Fastspänningsskruv |
   |   2 |Skruv med fyrkantigt hål framräckt skruv |
   |   3 |Vänster skena främre plats stift |
   |   4 |Fastspänningsskruv |
   |   5 |Vänster skena bakre plats stift |

### <a name="mounting-the-device-in-the-rack"></a>Montering av enheten i racket
Använd rackskenor som just installerades, utför följande steg för att montera enheten i racket.

#### <a name="to-mount-the-device"></a>Så här monterar du enheten
1. Lyft höljet med en assistent och rikta in den mot rackskenorna.
2. Sätt försiktigt in enheten i skenorna och tryck sedan in enheten helt i rackskåpet.<br/>
   
    ![Sätta in enheten i racket](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montering av enheten i racket**
3. Ta bort vänster och höger främre flänslock genom att dra mössorna fria. Flänslocken snäpper helt enkelt på flänsarna.
4. Säkra höljet i racket genom att installera en medföljande Phillips-huvudskruv genom varje fläns, vänster och höger.
5. Montera flänslocken genom att trycka dem på plats och knäppa dem på plats.<br/>
   
     ![Installera flänslock](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänslocken**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Fästskruv för kapsling |

Nästa steg är att kabel-din enhet för ström, nätverk och seriell åtkomst.

## <a name="cable-your-storsimple-8100-device"></a>Kabelansluta en StorSimple 8100-enhet
Följande procedurer förklarar hur du kabel din StorSimple 8100-enhet för ström-, nätverks- och seriella anslutningar.

### <a name="prerequisites"></a>Krav
Innan du börjar kabelansluta enheten behöver du:

* Din lagringsenhet, helt uppackad och rackmonterad.
* 2 strömkablar som följde med enheten
* Tillgång till 2 kraftdistributionsenheter (rekommenderas).
* Nätverkskablar
* Förutsatt seriekablar
* Seriell USB-omvandlare med lämplig drivrutin installerad på datorn (om det behövs)
* 4 QSFP-till-SFP+-kort för användning med 10 GbE-nätverksgränssnitt
* [Maskinvara som stöds för 10 GbE-nätverksgränssnitten på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Strömkablar
Enheten innehåller redundanta ström- och kylmoduler (PCM). Båda pcm måste installeras och anslutas till olika strömkällor för att säkerställa hög tillgänglighet.

Utför följande steg för att kabela enheten för ström.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablage
Enheten är en konfiguration med aktiv vänteläge: när som helst är en styrenhetsmodul aktiv och bearbetar alla disk- och nätverksåtgärder medan den andra styrenhetsmodulen är i vänteläge. Om en styrenhet misslyckas aktiveras standby-styrenheten omedelbart och fortsätter alla disk- och nätverksåtgärder.

Om du vill stödja den här redundanta redundansen måste du kabela enhetens nätverk enligt beskrivningen i följande steg.

#### <a name="to-cable-for-network-connection"></a>Till kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje styrenhet: fyra 1 Gbit/s och två Ethernet-portar på 10 Gbit/s. Identifiera de olika dataportarna på enhetens bakplan.
   
    ![Bakplan på 8100-enhet](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Baksidan av enheten som visar dataportar**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverksgränssnitt |
   |   2,3 |10 GbE-nätverksgränssnitt |
   |   6 |Serieportar |
2. Se följande diagram för nätverksblar. (Den minsta nätverkskonfigurationen visas med heldragna blå linjer. Ytterligare konfiguration som krävs för hög tillgänglighet och prestanda visas av prickade linjer.)

    ![Kabel din 2U-enhet för nätverk](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Nätverksblar för enheten**

   |Label (Etikett) | Beskrivning |
   |----- | ----------- |
   | A    | LAN med internetåtkomst |
   | B    | Kontrollant 0 |
   | C    | PCM 0 |
   | D    | Kontrollant 1 |
   | E    | PCM 1 |
   | F, G | Värdar |
   | 0-5  | Nätverksgränssnitt |



När du kablar enheten, kräver den minsta konfigurationen:

* Minst två nätverksgränssnitt som är anslutna på varje styrenhet med en för molnåtkomst och ett för iSCSI. DATA 0-porten aktiveras och konfigureras automatiskt via enhetens seriella konsol. Förutom DATA 0 måste en annan dataport också konfigureras via den klassiska Azure-portalen. I det här fallet ansluter du DATA 0-porten till det primära nätverket (nätverk med Internet-åtkomst). De andra dataportarna kan anslutas till SAN/iSCSI LAN (VLAN) i nätverket, beroende på vilken roll det är tänkt.
* Identiska gränssnitt på varje styrenhet som är ansluten till samma nätverk för att säkerställa tillgänglighet om en redundansväxling för styrenheten inträffar. Om du till exempel väljer att ansluta DATA 0 och DATA 3 för en av styrenheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Tänk på hög tillgänglighet och prestanda:

* Konfigurera om möjligt ett par nätverksgränssnitt för molnåtkomst (1 GbE) och ett annat par för iSCSI (10 GbE rekommenderas) på varje styrenhet.
* När det är möjligt ansluter du nätverksgränssnitt från varje styrenhet till två olika växlar för att säkerställa tillgänglighet mot ett växelfel. Figuren illustrerar de två 10 GbE-nätverksgränssnitten, DATA 2 och DATA 3, från varje styrenhet som är ansluten till två olika växlar.

Mer information finns i **nätverksgränssnitten** under [kraven för hög tillgänglighet för din StorSimple-enhet](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP+-sändtagare med dina 10 GbE-nätverksgränssnitt använder du de medföljande QSFP-SFP+-korten. Mer information finns i [Maskinvara som stöds för 10 GbE-nätverksgränssnitten på Din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Seriell port kablage
Utför följande steg för att kabel din seriella port.

#### <a name="to-cable-for-serial-connection"></a>Till kabel för seriell anslutning
1. Enheten har en seriell port på varje handkontroll som identifieras av en skiftnyckelikon. Se bilden i avsnittet [Nätverksblar](#network-cabling) för att hitta serieportarna på enhetens bakplan.
2. Identifiera den aktiva handkontrollen på enhetens bakplan. En blinkande blå lysdiod indikerar att handkontrollen är aktiv.
3. Använd de medföljande seriella kablarna (om det behövs, USB-seriell omvandlare för din bärbara dator) och anslut konsolen eller datorn (med terminal emulering till enheten) till den seriella porten för den aktiva styrenheten.
4. Installera serie-USB-drivrutiner (levereras med enheten) på datorn.
5. Ställ in den seriella anslutningen enligt följande: 115 200 baud, 8 databitar, 1 stoppbit, ingen paritet och flödeskontroll inställd på Ingen.
6. Kontrollera att anslutningen fungerar genom att trycka på Retur på konsolen. En seriell konsolmeny ska visas.

> [!NOTE]
> **Lights-Out Management**: När enheten är installerad i ett fjärrdatacenter eller i ett datorrum med begränsad åtkomst, se till att de seriella anslutningarna till båda styrenheterna alltid är anslutna till en seriell konsolswitch eller liknande utrustning. Detta möjliggör out-of-band fjärrkontroll och support om det finns nätverksavbrott eller oväntade fel.
> 
> 

Enheten är nu kabelansluten för ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran och distribuera enheten.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [distribuerar och konfigurerar din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

