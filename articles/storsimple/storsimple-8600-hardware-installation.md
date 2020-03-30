---
title: Installera Microsoft Azure StorSimple 8600-enhet
description: Beskriver hur du packar upp, rackfäste och kabel din StorSimple 8600-enhet innan du distribuerar och konfigurerar programvaran.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254760"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Packa upp, rackmontera och kabel din StorSimple 8600-enhet

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8600 är en enhet med dubbla höljen och består av en primär och en EBOD-hölje. I den här självstudien beskrivs hur du packar upp, rackmonterar och kabelrubjerar StorSimple 8600-enhetens maskinvara innan du konfigurerar StorSimple-programvaran.

## <a name="unpack-your-storsimple-8600-device"></a>Packa upp din StorSimple 8600-enhet
Följande steg innehåller tydliga, detaljerade instruktioner om hur du packar upp lagringsenheten StorSimple 8600. Den här enheten levereras i två lådor, en för den primära höljet och en annan för EBOD-höljet. Dessa två lådor placeras sedan i en enda låda.

### <a name="prepare-to-unpack-your-device"></a>Förbered att packa upp enheten
Läs följande information innan du packar upp enheten.

![Varning](./media/storsimple-safety/IC740879.png)![Icon tung](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) vikt ikon **VARNING!**

1. Se till att du har två personer tillgängliga för att hantera enhetens vikt om du hanterar den manuellt. En fullt konfigurerad kapsling kan väga upp till 32 kg.
2. Placera lådan på en plan, jämn yta.

Slutför sedan följande steg för att packa upp enheten.

#### <a name="to-unpack-your-device"></a>Så här packar du upp enheten
1. Kontrollera lådan och förpackningsskummet för att se om det förekommer krosskador, skärningar, vattenskador eller andra uppenbara skador. Om lådan eller förpackningen är allvarligt skadad ska du inte öppna lådan. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att hjälpa dig att bedöma om enheten är i gott skick.
2. Öppna den yttre rutan och ta sedan ut de två rutorna som motsvarar primära och EBOD-kapslingar. Du kan nu packa upp de primära och EBOD-kapslingarna. Följande bild visar den uppackade vyn för en av kapslingarna.
   
    ![Packa upp lagringsenheten](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Uppackad vy av lagringsenheten**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Packbox |
   |   2 |SAS-kablar (i tillbehörs- och kabelfack) |
   |   3 |Botten skum |
   |   4 |Enhet |
   |   5 |Övre skum |
   |   6 |Tillbehörslåda |
3. När du har packat upp de två rutorna kontrollerar du att du har:
   
   * 1 primär kapsling (den primära kapslingen och EBOD-kapslingen finns i två separata rutor)
   * 1 EBOD-kapsling
   * 4 nätsladdar, 2 i varje låda
   * 2 SAS-kablar (för att ansluta det primära höljet till EBOD-kapsling)
   * 1 korsad Ethernet-kabel
   * 2 seriella konsolkablar
   * 1 serie-USB-omvandlare för seriell åtkomst
   * 4 QSFP-till-SFP+-kort för användning med 10 GbE-nätverksgränssnitt
   * 2 rackmonteringssatser (4 sidoskenor med monteringsbeslag, 2 vardera för primärhöljet och EBOD-kapslingen), 1 i varje låda
   * Komma igång-dokumentation
     
     Om du inte har fått något av objekten i listan ovan [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Nästa steg är att rackmontera enheten.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rackmontera Din StorSimple 8600-enhet
Följ nästa steg för att installera lagringsenheten StorSimple 8600 i ett 19-tums standardställ med främre och bakre stolpar. Den här enheten levereras med två höljen: en primär kapsling och ett EBOD-hölje. Båda dessa måste rackmonteras.

Installationen består av flera steg, som var och en diskuteras i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för korrekt drift.
> 
> 

### <a name="site-preparation"></a>Förberedelse av plats
Kapslingarna måste installeras i ett standardställ på 19 tum som har både främre och bakre stolpar. Använd följande procedur för att förbereda för rackinstallation.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Så här förbereder du platsen för rackinstallation
1. Se till att primär- och EBOD-kapslingarna vilar säkert på en plan, stabil och jämn arbetsyta (eller liknande).
2. Kontrollera att platsen där du tänker ställa in har standardström från en oberoende källa eller en rackkraftfördelningsenhet (PDU) med en avbrottsfri strömförsörjning (UPS).
3. Se till att en 4U-plats (2 X 2U) finns på det rack där du tänker montera kapslingarna.

![Varning](./media/storsimple-safety/IC740879.png)![Icon tung](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) vikt ikon **VARNING!**

 Se till att du har två personer tillgängliga för att hantera vikten om du hanterar enhetskonfigurationen manuellt. En fullt konfigurerad kapsling kan väga upp till 32 kg.

### <a name="rack-prerequisites"></a>Rack förutsättningar
Kapslingarna är konstruerade för installation i ett 19-tums rackskåp med standard 19-tums rackskåp med:

* Minsta djup på 27,84 inches från rackstolpe till post
* Maximal vikt på 32 kg för enheten
* Maximalt mottryck på 5 Pascal (0,5 mm vattenmätare)

### <a name="rack-mounting-rail-kit"></a>Rackmonteringsskenasats
En uppsättning monteringsskenor kommer att tillhandahållas för användning med 19-tums rackskåp. Skenorna har testats för att hantera den maximala kapslingsvikten. Dessa skenor kommer också att möjliggöra installation av flera kapslingar utan förlust av utrymme i racket. Installera EBOD-höljet först.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Så här installerar du EBOD-kapslingen på rälsen
1. Utför bara det här steget om inre skenor inte är installerade på enheten. Vanligtvis installeras de inre skenorna på fabriken. Om skenor inte är installerade, installera sedan vänster-skena och höger-skena diabilder på sidorna av kapsling chassit. De fäster med sex metriska skruvar på varje sida. För att hjälpa till med orientering, är skenrutschbanorna **märkta LH - Front** och **RH - Front**, och den ände som är fäst mot baksidan av inneslutningen har en avsmalnande ände.
   
    ![Fästa rälsglas till kapslingschassi](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Fästa skenor glider på sidorna av inneslutningen**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |  1 |M 3x4 knapphuvud skruvar |
   |  2 |Chassiglas |
2. Fäst vänster skena och höger skena enheter till rackskåpet vertikala medlemmar. Hakparenteserna är märkta **LH,** **RH**och **Den här sidan uppåt** för att guida dig genom rätt orientering.
3. Leta upp spårstiften på framsidan och baksidan av spårenheten. Förläng skenan för att passa mellan rackstolparna och för in stiften i de främre och bakre rackstolpens vertikala medlemshål. Se till att skenenheten är jämn.
4. Säkra skenenheten till rackvertredarna med hjälp av två av de måttskruvar som tillhandahålls. Använd en skruv på framsidan och en på baksidan.
5. Upprepa dessa steg för den andra rälsenheten.
   
     ![Fästa rälsrutschbanor på rackskåp](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Fästa rälsenheter till racket**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Fastspänningsskruv |
   |   2 |Skruv med fyrkantigt hål framräckt skruv |
   |   3 |Vänster främre skena plats stift |
   |   4 |Fastspänningsskruv |
   |   5 |Vänster bakre skena plats stift |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montering av EBOD-höljet i racket
Använd rackskenorna som just installerades, utför följande steg för att montera EBOD-höljet i racket.

#### <a name="to-mount-the-ebod-enclosure"></a>Så här monterar du EBOD-höljet
1. Lyft höljet med en assistent och rikta in den mot rackskenorna.
2. Sätt försiktigt in höljet i skenorna och tryck sedan in den helt i rackskåpet.
   
    ![Sätta in enheten i racket](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montering av höljet i racket**
3. Ta bort vänster och höger främre flänslock genom att dra mössorna fria. Flänslocken snäpper helt enkelt på flänsarna.
4. Säkra höljet i racket genom att installera en medföljande Phillips-huvudskruv genom varje fläns, vänster och höger.
5. Montera flänslocken genom att trycka dem på plats och knäppa dem på plats.
   
     ![Installera flänslock](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänslocken**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   1 |Fästskruv för kapsling |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montering av primärhöljet i racket
När du är klar med monteringen av EBOD-höljet måste du montera den primära höljet enligt samma steg.

> [!NOTE]
> * Det är möjligt att ha några tomma spår i racket mellan den primära inneslutningen och EBOD-kapslingen.
> * Använd den medföljande 2m SAS-kabeln för att ansluta det primära höljet till EBOD-höljet.
> * Det finns inga begränsningar för huvudenhetens relativa placering på EBOD-enheten. Därför kan den primära kapslingen placeras i den övre kortplatsen och EBOD-höljet nedan – eller vice versa.
> 
> 

Nästa steg är att kabel-din enhet för ström, nätverk och seriell åtkomst.

## <a name="cable-your-storsimple-8600-device"></a>Kabelansluta en StorSimple 8600-enhet
Följande procedurer förklarar hur du kabel din StorSimple 8600-enhet för ström-, nätverks- och seriella anslutningar.

### <a name="prerequisites"></a>Krav
Innan du börjar kabel-tv-enheten behöver du:

* Din primära hölje och EBOD-höljet, helt uppackad
* 4 strömkablar (2 vardera för primär- och EBOD-höljet) som följde med enheten
* 2 SAS-kablar som medföljer enheten för att ansluta EBOD-höljet till det primära höljet
* Tillgång till 2 kraftdistributionsenheter (rekommenderas)
* Nätverkskablar
* Förutsatt seriekablar
* Serial-USB-omvandlare med lämplig drivrutin installerad på datorn (om det behövs)
* 4 QSFP-till-SFP+-kort för användning med 10 GbE-nätverksgränssnitt
* [Maskinvara som stöds för 10 GbE-nätverksgränssnitten på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS och Power kablage
Enheten har både en primär hölje och en EBOD-hölje. Detta kräver att enheterna kopplas ihop för SAS-anslutning (Serial Attached SCSI) och ström.

När du konfigurerar den här enheten för första gången utför du stegen för SAS-kablar först och slutför sedan stegen för strömkablar.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablage
Enheten är i en konfiguration med aktiv vänteläge: när som helst är en styrenhetsmodul aktiv och bearbetar alla disk- och nätverksåtgärder medan den andra styrenhetsmodulen är i vänteläge. Om ett styrenhetsfel inträffar aktiveras och fortsätter alla disk- och nätverksåtgärder.

För att stödja denna redundanta kontrollant redundans, måste du kabel enhetens nätverk som visas i följande steg.

#### <a name="to-cable-for-network-connection"></a>Till kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje styrenhet: fyra 1 Gbit/s och två Ethernet-portar på 10 Gbit/s. Se följande bild för att identifiera dataportarna på enhetens bakplan.
   
     ![Bakplan på 8600-enhet](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Baksidan av enheten som visar dataportarna**
   
   | Label (Etikett) | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverksgränssnitt |
   |   2,3 |10 GbE-nätverksgränssnitt |
   |   6 |Serieportar |
2. Se följande diagram för nätverksblar. (Den minsta nätverkskonfigurationen visas med heldragna blå linjer. För hög tillgänglighet och prestanda visas ytterligare konfiguration som krävs av prickade linjer.)

![Kabel din 4U-enhet för nätverk](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Nätverksblar för enheten**

| Label (Etikett) | Beskrivning |
| --- | --- |
| A |LAN med internetåtkomst |
| B |Kontrollant 0 |
| C |PCM 0 |
| D |Kontrollant 1 |
| E |PCM 1 |
| F |EBOD-styrenhet 0 |
| G |EBOD-styrenhet 1 |
| H, jag |Värdar (till exempel filservrar) |
| 0-5 |Nätverksgränssnitt |
| 6 |Primär kapsling |
| 7 |EBOD-kapsling |

När du kablar enheten, kräver den minsta konfigurationen:

* Minst två nätverksgränssnitt som är anslutna på varje styrenhet med en för molnåtkomst och ett för iSCSI. DATA 0-porten aktiveras och konfigureras automatiskt via enhetens seriella konsol. Förutom DATA 0 måste en annan dataport också konfigureras via den klassiska Azure-portalen. I det här fallet ansluter du DATA 0-porten till det primära nätverket (nätverk med Internet-åtkomst). De andra dataportarna kan anslutas till SAN/iSCSI LAN (VLAN) i nätverket, beroende på vilken roll det är tänkt.
* Identiska gränssnitt på varje styrenhet som är ansluten till samma nätverk för att säkerställa tillgänglighet om en redundansväxling för styrenheten inträffar. Om du till exempel väljer att ansluta DATA 0 och DATA 3 för en av styrenheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Tänk på hög tillgänglighet och prestanda:

* Konfigurera om möjligt ett par nätverksgränssnitt för molnåtkomst (1 GbE) och ett annat par för iSCSI (10 GbE rekommenderas) på varje styrenhet.
* När det är möjligt ansluter du nätverksgränssnitt från varje styrenhet till två olika växlar för att säkerställa tillgänglighet mot ett växelfel. Figuren illustrerar de två 10 GbE-nätverksgränssnitten, DATA 2 och DATA 3, från varje styrenhet som är ansluten till två olika växlar. Mer information finns i **nätverksgränssnitten** under [kraven för hög tillgänglighet för din StorSimple-enhet](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om du använder SFP+-sändtagare med dina 10 GbE-nätverksgränssnitt använder du de medföljande QSFP-SFP+-korten. Mer information finns i [Maskinvara som stöds för 10 GbE-nätverksgränssnitten på Din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Seriell port kablage
Utför följande steg för att kabel din seriella port.

#### <a name="to-cable-for-serial-connection"></a>Till kabel för seriell anslutning
1. Enheten har en seriell port på varje handkontroll som identifieras av en skiftnyckelikon. Om du vill hitta serieportarna läser du bilden som visar dataportarna på baksidan av enheten.
2. Identifiera den aktiva handkontrollen på enhetens bakplan. En blinkande blå lysdiod indikerar att handkontrollen är aktiv.
3. Använd den medföljande seriekabeln (om det behövs, USB-seriell omvandlare för din bärbara dator) och anslut konsolen eller datorn (med terminal emulering till enheten) till den seriella porten för den aktiva styrenheten.
4. Installera serie-USB-drivrutiner (levereras med enheten) på datorn.
5. Ställ in den seriella anslutningen enligt följande:
   
   * 115.200 baud
   * 8 databitar
   * 1 stoppbit
   * Ingen paritet
   * Flödeskontroll inställd på **Ingen**
6. Kontrollera att anslutningen fungerar genom att trycka på Retur på konsolen. En seriell konsolmeny ska visas.

> [!NOTE]
> **Hantering av lights-out:** När enheten är installerad i ett fjärrdatacenter eller i ett datorrum med begränsad åtkomst, se till att de seriella anslutningarna till båda styrenheterna alltid är anslutna till en seriell konsolswitch eller liknande utrustning. Detta möjliggör out-of-band fjärrkontroll och support åtgärder i händelse av nätverksavbrott eller oväntade fel.
> 
> 

Du har slutfört kablarna till enheten för ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran på enheten.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

