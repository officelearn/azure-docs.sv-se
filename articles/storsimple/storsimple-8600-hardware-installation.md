---
title: Installera Microsoft Azure StorSimple 8600-enhet | Microsoft Docs
description: Beskriver hur du packa upp, rackmontera och kabelanslut din 8600 StorSimple-enhet innan du distribuerar och konfigurerar programvaran.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/24/2016
ms.author: alkohli
ms.openlocfilehash: 309ceba2d65c0745ba1acac698acb62526ab8078
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Packa upp, rackmontera, och kabelanslut din 8600 StorSimple-enhet
## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8600-enhet är en enhet med dubbla höljet och består av en primär och en EBOD bilaga. Den här självstudiekursen beskrivs hur du packa upp, rackmonterad och kabel StorSimple 8600-enhet maskinvara innan du konfigurerar StorSimple-programvara.

## <a name="unpack-your-storsimple-8600-device"></a>Packa upp din StorSimple-8600-enhet
Följande steg ger tydliga, detaljerade instruktioner för hur du packar lagringsenheten StorSimple 8600. Den här enheten levereras i två rutor, en för primära höljet och en annan för EBOD höljet. Dessa två rutor placeras sedan i en enda ruta.

### <a name="prepare-to-unpack-your-device"></a>Förbereda för att packa upp din enhet
Granska följande information innan du packar upp din enhet.

![Varningsikon](./media/storsimple-safety/IC740879.png)![tunga vikt ikonen](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

1. Kontrollera att du har två personer som är tillgängliga för att hantera vikten av enheten om hanterar manuellt. Ett helt konfigurerade hölje kan väga upp till 32 kg (70 pund).
2. Placeras på en plan, nivå-yta.

Därefter utför följande steg för att packa upp din enhet.

#### <a name="to-unpack-your-device"></a>Att packa upp din enhet
1. Granska rutan och paketering skummet för crushes delar, vattenstämplar skador eller andra uppenbara skador. Om rutan eller paketering är allvarligt skadat kan inte öppna rutan. Kontrollera [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) som hjälper dig att bedöma om enheten är i gott skick.
2. Öppna rutan yttre och sedan ta ut i två rutor som motsvarar den primära servern och EBOD höljen. Du kan nu packa upp den primära servern och EBOD höljen. Följande bild visar den uppackade en höljena.
   
    ![Packa upp lagringsenheten](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Packa upp visning av lagringsenheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Packkorg rutan |
   |   2 |SAS-kablar (i tillbehör och kablar fack) |
   |   3 |Nedre skum |
   |   4 |Enhet |
   |   5 |Övre skum |
   |   6 |Tillbehör rutan |
3. Kontrollera att du har när de två rutorna:
   
   * 1 primära hölje (primära höljet och EBOD hölje är i två separata rutorna)
   * 1 EBOD hölje
   * 4 strömkablar, 2 i respektive ruta
   * 2 SAS-kablar (för att ansluta det primära höljet till EBOD hölje)
   * 1 övergång Ethernet-kabel
   * 2 seriekonsolen kablar
   * 1 seriell USB konverterare för seriell åtkomst
   * 4 QSFP-till-SFP + kort för användning med 10 GbE-nätverkskort
   * 2 rack montera kits (4 sida spår med montera maskinvara, 2 för den primära höljet och EBOD hölje), 1 i respektive ruta
   * Komma igång-dokumentationen
     
     Om du inte tog emot något av objekten i listan ovan, [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).  

Nästa steg är att rackmonterade enheten.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rackmonterade din StorSimple-8600-enhet
Följ stegen nedan om du vill installera lagringsenheten StorSimple 8600 i en standard 19-tums rack med främre och bakre meddelandena. Den här enheten levereras med två höljen: en primär enhet och en EBOD bilaga. Båda dessa måste vara rackmonterade.

Installationen består av flera steg, som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för att fungera korrekt.
> 
> 

### <a name="site-preparation"></a>Förberedelse
Höljena måste installeras i ett standard 19-tums rack som har både främre och bakre meddelandena. Använd följande procedur för att förbereda för Rackinstallation av.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Så här förbereder du platsen för Rackinstallation
1. Se till att den primära servern och EBOD höljen är vilande på ett säkert sätt på en flat stabilt och nivå arbetsyta (eller liknande).
2. Kontrollera att platsen där du vill konfigurera har standard AC-ström från en oberoende källa eller ett rack Power Distribution enhet (PDU) med en avbrottsfri elkälla (UPS).
3. Kontrollera att en 4U (2 X 2U) platsen är tillgänglig på rack som du vill montera höljena.

![Varningsikon](./media/storsimple-safety/IC740879.png)![tunga vikt ikonen](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

 Kontrollera att du har två personer som är tillgängliga för att hantera vikten om du hantering av Enhetsinställningar manuellt. Ett helt konfigurerade hölje kan väga upp till 32 kg (70 pund).

### <a name="rack-prerequisites"></a>Rack krav
Höljena är utformade för installation i en standard 19-tums rack CAB med:

* Minsta djup 27.84 tum från rack efter post
* Högsta vikt för 32 kg för enheten
* Maximal som ligger på 5 Pascal (0,5 mätare)

### <a name="rack-mounting-rail-kit"></a>Rack montering tåg kit
En uppsättning montera spår anges för användning med 19-tums rack kabinettfilen. Spår har testats för att hantera största höljet vikt. Dessa spår kommer också att tillåta installation av flera höljen utan dataförlust utrymme i racket. Installera EBOD höljet först.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Installera EBOD höljet i spår
1. Utför det här steget endast om inre spår inte har installerats på enheten. Inre spår installeras vanligtvis på fabriken. Om spår inte har installerats, installerar du tåg vänster och höger tåg bilder till sidorna på höljet chassi. De fäster med sex mått skruvar på varje sida. För att hjälpa med orientering tåg bilder markeras **LH – fram** och **RH – fram**, och end som fästs bakåt från höljet har en avsmalnande end.
   
    ![Bifoga tåg bilder höljet chassi](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Kopplar tåg bilder till sidorna på höljet**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |  1 |M 3 x 4 knappen head skruvar |
   |  2 |Chassi bilder |
2. Bifoga tåg vänster och höger sammansättningar rack CAB lodräta medlemmar. Hakparenteserna markeras **LH**, **RH**, och **den här sidan uppåt** som hjälper dig att rätt orientering.
3. Leta upp PIN-koder för tåg på främre och bakre tåg sammansättningens. Utöka spåret för att passa mellan rack meddelandena och infoga de PIN-koderna i främre och bakre rack post lodräta medlem hål. Se till att sammansättningen tåg är nivå.
4. Skydda tåg sammansättningen till racket lodräta medlemmar genom att två av de mått skruvarna tillhandahålls. Använd en skruv på framsidan och en på Bakåt.
5. Upprepa dessa steg för andra tåg sammansättningen.
   
     ![Kopplar tåg bilder till rack CAB](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Kopplar tåg sammansättningar till racket**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Minskning skruv |
   |   2 |Ruta hål främre rack post skruv |
   |   3 |Vänster främre tåg plats PIN-koder |
   |   4 |Minskning skruv |
   |   5 |Vänster bakre tåg plats PIN-koder |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montera EBOD höljet i racket
Använd rack spår som precis har installerats och utför följande steg för att montera EBOD höljet i racket.

#### <a name="to-mount-the-ebod-enclosure"></a>Montera EBOD höljet
1. Med en assistent lyfter höljet och justera med rack spår.
2. Infoga noggrant höljet i spår och sedan push-installera den helt i racket cab.
   
    ![Lägga till enheten i racket](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera höljet i racket**
3. Ta bort vänster och höger främre flänsad caps genom att dra caps ledigt. Flänsad caps Fäst bara till flänsar.
4. Skydda höljet i racket genom att installera en angivna stjärnskruvmejsel head skruv via varje flänsad vänster och höger.
5. Installera flänsad caps genom att trycka ned dem till rätt plats och koppla dem till rätt plats.
   
     ![Installera flänsad caps](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänsad caps**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Höljet fästanordning skruv |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montera primära höljet i racket
När du är klar med att montera EBOD höljet måste montera primära höljet följa samma steg.

> [!NOTE]
> * Det är möjligt att ha några tomma platser i racket mellan primära höljet och EBOD höljet.
> * Använd den angivna 2m SAS-kabeln för att ansluta primära höljet till EBOD höljet.
> * Det finns inga begränsningar för relativa placeringen av head enheten till EBOD-enhet. Därför primära höljet kan placeras i den översta platsen och EBOD höljet nedan, och vice versa.
> 
> 

Nästa steg är att kabelanslut din enhet till ström, nätverk och serieåtkomst.

## <a name="cable-your-storsimple-8600-device"></a>Kabelanslut din 8600 StorSimple-enhet
Följande procedurer beskriver hur du kabelanslut din 8600 StorSimple-enhet till ström, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Krav
Innan du börjar kabelanslut din enhet, behöver du:

* Din primära höljet och höljet EBOD helt Uppackad
* 4 strömkablar (2 varje för den primära servern och EBOD höljet) som medföljde din enhet
* 2 SAS-kablar medföljer att enheten ansluter EBOD höljet till primära höljet
* Åtkomst till 2 Kraftfördelningsenheter (PDU) (rekommenderas)
* Nätverkskablarna
* Angivna seriella kablar
* Seriell USB-konverteraren med lämplig drivrutin installeras på din dator (om det behövs)
* Angivna 4 QSFP-till-SFP + kort för användning med 10 GbE-nätverkskort
* [Maskinvara som stöds för 10 GbE-nätverkskort på din StorSimple-enhet](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS- och kablar
Enheten har både en primär enhet och en EBOD bilaga. Detta kräver att enheter ska vara kabelanslutna tillsammans för seriellt ansluten SCSI (SAS)-anslutning och ström.

När du konfigurerar enheten för första gången, utför steg för SAS-kablar först och följer sedan anvisningarna för kablar.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablar
Enheten är i ett aktivt vänteläge konfiguration: samtidigt, en modul som domänkontrollanten är aktiv och bearbetning av alla disk och nätverk när modulen andra domänkontrollanter är i vänteläge. Om en domänkontrollant inträffar, aktiverar vänteläge controller omedelbart och fortsätter alla disk och nätverk åtgärder.

För att stödja den här redundansen redundant domänkontrollant, behöver du kabelanslut din enhet nätverk som visas i följande steg.

#### <a name="to-cable-for-network-connection"></a>Till kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje domänkontrollant: fyra 1 Gbit/s och två 10 Gbit/s Ethernet-portarna. Finns i följande bild för att identifiera dataportar på bakplan av enheten.
   
     ![Bakplan av 8600-enhet](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Visar portarna som data tillbaka av enheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverkskort |
   |   2,3 |10 GbE-nätverkskort |
   |   6 |Seriella portar |
2. Finns i följande diagram på nätverkskablar. (Minsta nätverkskonfigurationen visas med blå heldragen linje. För hög tillgänglighet och prestanda visas ytterligare konfiguration krävs med streckade linjer.)

![Kabelansluta den 4U för nätverket](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Nätverket kablar för din enhet**

| Etikett | Beskrivning |
| --- | --- |
| A |LAN med Internetåtkomst |
| B |Styrenhet 0 |
| C |PCM 0 |
| D |Kontrollant 1 |
| E |PCM 1 |
| F |EBOD styrenhet 0 |
| G |EBOD kontrollant 1 |
| H, JAG |Värdar (till exempel filservrar) |
| 0-5 |Nätverksgränssnitt |
| 6 |Primär enhet |
| 7 |EBOD hölje |

När kablar enheten kräver den lägsta konfigurationen:

* Minst två nätverkskort som är anslutna på varje domänkontrollant för att komma åt molnet och en för iSCSI. DATA 0 port aktiveras och konfigureras via seriekonsolen för enheten automatiskt. Förutom DATA 0 måste en annan dataport också konfigureras via den klassiska Azure-portalen. I det här fallet ansluta DATA 0 porten till den primära LAN (nätverk med åtkomst till Internet). Andra dataportar kan vara ansluten till SAN/iSCSI-LAN (VLAN)-segmentet i nätverket, beroende på vilken roll som avsett.
* Samma gränssnitt på varje domänkontrollant anslutna till samma nätverk för att säkerställa tillgänglighet om det uppstår redundans domänkontrollant. Till exempel om du vill ansluta DATA 0 och DATA 3 för en av domänkontrollanterna behöver du ansluta motsvarande DATA 0 och DATA 3 på en annan domänkontrollant.

Kom ihåg för hög tillgänglighet och prestanda:

* Konfigurera ett par med nätverksgränssnittet för molnåtkomst (1 GbE) och en annan par för iSCSI (10 GbE rekommenderas) på varje domänkontrollant när det är möjligt.
* När det är möjligt ansluta nätverksgränssnitt från varje domänkontrollant till två olika växlar för att garantera mot en switch-fel. Bilden visar två 10 GbE nätverksgränssnitt, DATA 2 och DATA 3 från varje domänkontrollant som är ansluten till två olika växlar. Mer information finns i den **nätverksgränssnitt** under den [krav på hög tillgänglighet för din StorSimple-enhet](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om använder SFP + mottagarna med din 10 GbE-nätverkskort, använder den angivna QSFP-SFP + nätverkskort. Mer information finns på [maskinvara som stöds för 10 GbE-nätverkskort på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Serieport kablage
Utför följande steg för att kabelanslut din serieport.

#### <a name="to-cable-for-serial-connection"></a>Att kabel för seriell anslutning
1. Enheten har en seriell port på varje domänkontrollant som identifieras av en skiftnyckelikonen. För att hitta seriella portar, referera till den bild som visar data portar på baksidan av din enhet.
2. Identifiera den aktiva styrenheten på din enhet bakplan. En blinkande blå Indikator anger att kontrollanten är aktiv.
3. Använd den angivna seriella kabeln (vid behov, USB-seriell konverteraren för din bärbara dator) och Anslut din dator (med terminalemulering till enheten) eller konsolen till seriell port för den aktiva styrenheten.
4. Installera serial-USB-drivrutiner (medföljer enheten) på datorn.
5. Ställ in seriell anslutning enligt följande:
   
   * 115 200 bit/s
   * 8 databitar
   * 1 stop-bitars
   * Ingen paritet
   * Flödeskontroll inställd på **ingen**
6. Kontrollera att anslutningen fungerar genom att trycka på RETUR på konsolen. Menyn för seriekonsolen ska visas.

> [!NOTE]
> **Lights-Out-hantering:** när enheten har installerats i en fjärransluten datacenter eller i en lokal dator med begränsad åtkomst, kontrollera att de seriella anslutningarna till både domänkontrollanter alltid är ansluten till en seriekonsolen växel eller liknande utrustning. Detta tillåter fjärrstyrning för out-of-band och stöd för åtgärder vid avbrott i nätverket eller oväntade fel.
> 
> 

Du har slutfört kablar enheten till ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran på din enhet.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-deployment-walkthrough-u2.md).

