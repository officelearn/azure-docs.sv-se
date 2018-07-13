---
title: Installera Microsoft Azure StorSimple 8600-enhet | Microsoft Docs
description: Beskriver hur du packa upp, rackmontera och kabelansluta en StorSimple 8600-enhet innan du distribuerar och konfigurerar programvaran.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5a8b460441323cb668a3d9939cce434636afc44d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719013"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Packa upp, rackmontera, och kabelansluta en StorSimple 8600-enhet
## <a name="overview"></a>Översikt
Din Microsoft Azure StorSimple 8600-enhet är en dubbel hölje enhet och består av en primär och en EBOD-kabinett. Den här självstudien beskrivs hur du packar upp, rackmontera och kabel StorSimple 8600-enhet maskinvara innan du konfigurerar programvaran från StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Packa upp din StorSimple 8600-enhet
Följande steg ger tydliga, detaljerade instruktioner om hur du packar upp lagringsenheten StorSimple 8600. Den här enheten levereras i två rutor, en för primära höljet och en annan för EBOD-höljet. Dessa två rutor placeras sedan i en enda ruta.

### <a name="prepare-to-unpack-your-device"></a>Förbereda för att packa upp din enhet
Granska följande information innan du packar upp enheten.

![Varningsikon](./media/storsimple-safety/IC740879.png)![kraftiga ikonen](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

1. Se till att du har två personer som är tillgängliga för att hantera vikten för enheten om du hanterar det manuellt. Ett fullständigt konfigurerad hölje kan väga upp till 32 kg (70 lbs.).
2. Placeras på en fast, på ytan.

Därefter gör du följande för att packa upp din enhet.

#### <a name="to-unpack-your-device"></a>Att packa upp din enhet
1. Granska rutan och paketering skummet för crushes, rabatter, vatten skador eller andra uppenbara skador. Om rutan eller paketering är allvarligt skadat kan inte öppna rutan. . [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att hjälpa dig att bedöma om enheten är i gott skick.
2. Öppna rutan yttre och sedan ta ut två kryssrutor som motsvarar primär och EBOD bilagor. Du kan nu packar upp den primära servern och EBOD-höljen. Följande bild visar vyn uppackat för en av höljena.
   
    ![Packa upp lagringsenheten](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **Packa upp vy över lagringsenheten**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Paketering av statistik box |
   |   2 |SAS-kablar (i fack tillbehör och kablar) |
   |   3 |Nedre skum |
   |   4 |Enhet |
   |   5 |Främsta skum |
   |   6 |Tillbehör box |
3. Kontrollera att du har när två rutor:
   
   * 1 primär hölje (primär lagringsenhet och EBOD hölje finns i två separata rutor)
   * 1 EBOD hölje
   * 4 strömkablar, 2 i varje ruta
   * 2 SAS-kablar (för att ansluta primära höljet till EBOD hölje)
   * 1 övergång Ethernet-kabel
   * 2 Seriell konsol-kablar
   * 1 seriell USB-konverterare för seriell åtkomst
   * 4 QSFP-till-SFP +-kort för användning med 10 GbE-nätverksgränssnitt
   * 2 rack montera kits (4 sida rails med montera maskinvara, 2 för primär lagringsenhet och EBOD hölje), 1 i varje ruta
   * Komma igång-dokumentation
     
     Om du inte tog emot något av objekten som visas ovan, [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

Nästa steg är att rackmonterade din enhet.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rackmonterade StorSimple 8600-enhet
Följ stegen nedan för att installera din StorSimple 8600-enhet för lagring i ett standard 19-tums rack med främre och bakre inlägg. Den här enheten levereras med två höljen: en primär lagringsenhet och ett EBOD-hölje. Båda dessa måste vara rackmonterade.

Installationen består av flera steg, som beskrivs i följande procedurer.

> [!IMPORTANT]
> StorSimple-enheter måste vara rackmonterade för att fungera korrekt.
> 
> 

### <a name="site-preparation"></a>Förberedelse
Höljena måste installeras i en standard 19-tums rack som har både främre och bakre inlägg. Använd följande procedur för att förbereda för Rackinstallation av.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Förbereda platsen för Rackinstallation
1. Se till att den primära servern och EBOD-höljen är vilande på ett säkert sätt på en fast, stabila och nivån arbetsyta (eller liknande).
2. Kontrollera att den plats där du vill konfigurera har standard nätström från en oberoende källa eller ett rack Power Distribution enhet (PDU) med en avbrottsfri elkälla (UPS).
3. Kontrollera att platsen för en 4U (2 X 2U) är tillgängliga på rack där du vill montera höljena.

![Varningsikon](./media/storsimple-safety/IC740879.png)![kraftiga ikonen](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **varning!**

 Se till att du har två personer som är tillgängliga för att hantera vikten om du hanterar installationen av enheten manuellt. Ett fullständigt konfigurerad hölje kan väga upp till 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Rack krav
Höljena är utformade för installation i en standard 19-tums rack CAB med:

* Minsta mängd 27.84 tum från rack efter att inlägget
* Högsta vikt för 32 kg för enheten
* Maximal tryck av 5 Pascal (0,5 mätare)

### <a name="rack-mounting-rail-kit"></a>Rack montering rail kit
En uppsättning montera rails ges för användning med 19-tums rack kabinettfilen. Rails har testats för att hantera maximala hölje vikt. Dessa rails kommer också att tillåta installation av flera höljen utan att förlora utrymme i racket. Installera EBOD-höljet först.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Installera EBOD-höljet på rails
1. Utför det här steget endast om inre rails inte är installerade på din enhet. De inre rails installeras vanligtvis på fabriken. Om rails inte installeras, installerar du vänster-spår och höger rail presentationen till sidor av hölje chassi. De bifoga sex mått skruvar på varje sida. För att hjälpa till med orientering, rail presentationen markeras **LH – Front** och **RH – Front**, och i slutet som fästs bakåt från höljet har en avsmalnande slutet.
   
    ![Bifoga rail bilder till hölje chassi](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Bifoga rail bilder till sidor av höljet**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |  1 |M 3 x 4 knappen head skruvar |
   |  2 |Chassi-bilder |
2. Koppla den vänstra spår och höger sammansättningar till rack CAB lodrät medlemmar. Hakparenteserna markeras **LH**, **RH**, och **den här sidan uppåt** att guida dig genom rätt orientering.
3. Leta upp PIN-koder för rail på den främre och bakre rail-sammansättningen. Utöka spår för att passa mellan rack inlägg och infoga de PIN-koderna i främre och bakre rack efter vertikal medlem hål. Var noga med att rail-sammansättningen är nivå.
4. Skydda rail sammansättningen till racket lodrät medlemmar med två av de mått skruvarna som tillhandahålls. Använd en skruv på klientwebbservern och en på Bakåt.
5. Upprepa dessa steg för den andra rail-sammansättningen.
   
     ![Bifoga rail bilder till rack CAB](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Koppla rail sammansättningar till racket**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Ihopfogning skruv |
   |   2 |Kvadrat hål front rack efter skruv |
   |   3 |Vänster främre rail plats PIN-koder |
   |   4 |Ihopfogning skruv |
   |   5 |Vänstra bakre rail plats PIN-koder |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montera EBOD-höljet i racket
Med de rack rails som precis har installerats kan utföra följande steg om du vill montera EBOD-höljet i racket.

#### <a name="to-mount-the-ebod-enclosure"></a>Montera EBOD-hölje
1. Med en assistent lyfta höljet och justera den med rack rails.
2. Infoga noggrant höljet i rails, och skicka sedan den helt i racket kabinettfil.
   
    ![Infoga enheten i racket](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Montera höljet i racket**
3. Ta bort vänster och höger främre flänsad caps genom att hämta caps kostnadsfritt. Flänsad caps Fäst helt enkelt till flänsar.
4. Skydda höljet i racket genom att installera en angiven Phillips head skruv via varje flänsad vänster och höger.
5. Installera flänsad caps genom att trycka på dem på plats och fästa dem på plats.
   
     ![Installera flänsad caps](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **Installera flänsad caps**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   1 |Hölje flygplanet skruv |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montera primära höljet i racket
När du är klar med att montera EBOD-höljet kommer du behöva montera primära höljet följa samma steg.

> [!NOTE]
> * Det är möjligt att ha några tomma platser i rack mellan primära höljet och EBOD-kabinett.
> * Använda den angivna 2m SAS-kabeln för att ansluta primära höljet till EBOD-höljet.
> * Det finns inga begränsningar i relativa placeringen av den head enheten till den EBOD-enheten. Därför primära höljet kan placeras i den översta platsen och EBOD höljet nedan – eller vice versa.
> 
> 

Nästa steg är att kabelanslut din enhet till ström, nätverk och serieåtkomst.

## <a name="cable-your-storsimple-8600-device"></a>Kabelansluta en StorSimple 8600-enhet
Följande procedurer beskriver hur du kabelansluta en StorSimple 8600-enhet till ström, nätverk och seriella anslutningar.

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar kabelanslut din enhet, behöver du:

* Din primära hölje och EBOD-kabinett helt Uppackad
* 4 strömkablar (2 vardera för den primära servern och EBOD-höljet) som medföljde din enhet
* 2 SAS-kablar som medföljer enheten att ansluta EBOD-höljet till det primära höljet
* Åtkomst till 2 Kraftfördelningsenheter (PDU) (rekommenderas)
* Nätverkskablar
* Angivna seriella kablar
* Seriell USB converter med lämplig drivrutin installeras på din dator (vid behov)
* Tillhandahålls 4 QSFP-till-SFP +-kort för användning med 10 GbE-nätverksgränssnitt
* [Maskinvara som stöds för 10 GbE-nätverksgränssnitt på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS och kablar
Enheten har både ett hölje som primär och en EBOD-kabinett. Detta kräver att enheter för att vara kabelansluten tillsammans till seriellt ansluten SCSI (SAS)-anslutning och ström.

När du konfigurerar den här enheten för första gången utför steg för SAS-kablar först och följer sedan anvisningarna för kablar.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Nätverkskablar
Enheten är aktiv-standby-konfiguration: vid en given tidpunkt, en domänkontrollant-modul är aktiv och bearbetning av alla åtgärder på disk och nätverk när modulen andra domänkontrollanter är i vänteläge. Om en domänkontrollant-fel uppstår, aktiveras kontrollenheten i vänteläge omedelbart och fortsätter alla disk och nätverk åtgärder.

Du måste kabelanslut din enhet-nätverk som du ser i följande steg för att stödja den här redundant kontrollenhetsredundans.

#### <a name="to-cable-for-network-connection"></a>Att kabel för nätverksanslutning
1. Enheten har sex nätverksgränssnitt på varje domänkontrollant: fyra 1 Gbit/s och två 10 Gbit/s Ethernet-portar. Se följande bild för att identifiera vilka dataportar på serverdelen för din enhet.
   
     ![Serverdelen av 8600-enhet](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Tillbaka på din enhet visar dataportar**
   
   | Etikett | Beskrivning |
   | --- | --- |
   |   0,1,4,5 |1 GbE-nätverksgränssnitt |
   |   2,3 |10 GbE-nätverksgränssnitt |
   |   6 |Seriella portar |
2. Se i följande diagram nätverkskablar. (Lägsta nätverkskonfigurationen visas som heldragna blå linjer. För hög tillgänglighet och prestanda visas ytterligare konfiguration krävs av kantlinjerna.)

![Kabelansluta den 4U för nätverk](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Nätverket kablar för din enhet**

| Etikett | Beskrivning |
| --- | --- |
| A |LAN med Internetåtkomst |
| B |Kontrollenhet 0 |
| C |PCM 0 |
| D |Kontrollenhet 1 |
| E |PCM 1 |
| F |EBOD-kontrollanten 0 |
| G |EBOD-kontrollanten 1 |
| H, JAG |Värdar (till exempel filservrar) |
| 0-5 |Nätverksgränssnitt |
| 6 |Primär hölje |
| 7 |EBOD hölje |

När kablar enheten kräver den lägsta konfigurationen:

* Minst två nätverksgränssnitt är anslutna på varje domänkontrollant för att komma åt cloud och en för iSCSI. DATA 0 port aktiveras och konfigureras via seriekonsolen för enheten automatiskt. Förutom DATA 0 måste en annan dataporten också konfigureras via den klassiska Azure-portalen. I det här fallet ansluta DATA 0 porten till den primära LAN (nätverk med åtkomst till Internet). Andra dataportar kan anslutas till SAN/iSCSI LAN (VLAN)-segmentet i nätverket, beroende på den avsedda rollen.
* Samma gränssnitt på varje domänkontrollant är anslutna till samma nätverk för att säkerställa tillgänglighet om det uppstår redundans controller. Exempelvis om du vill ansluta DATA 0 och DATA 3 för en av kontrollenheterna måste du ansluta motsvarande DATA 0 och DATA 3 på den andra styrenheten.

Ha i åtanke för hög tillgänglighet och prestanda:

* Konfigurera ett par med nätverksgränssnitt för molnåtkomst (1 GbE) och en annan par för iSCSI (10 GbE rekommenderas) på varje domänkontrollant när det är möjligt.
* Om det är möjligt ska du ansluta nätverksgränssnitt från varje styrenhet till två olika växlar för att säkerställa tillgänglighet mot ett switch-fel. Bilden visar två 10 GbE nätverksgränssnitt, DATA 2 och DATA 3 från varje domänkontrollant som är ansluten till två olika växlar. Mer information finns i den **nätverksgränssnitt** under den [krav på hög tillgänglighet för StorSimple-enheten](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Om använder SFP + mottagarna med din 10 GbE-nätverksgränssnitt använder den angivna QSFP-SFP +-kort. Mer information går du till [maskinvara som stöds för 10 GbE-nätverksgränssnitt på StorSimple-enheten](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Seriell port kablar
Utför följande steg för att kabelanslut din seriell port.

#### <a name="to-cable-for-serial-connection"></a>Att kabel för seriell anslutning
1. Enheten har en seriell port på varje domänkontrollant som identifieras med en skiftnyckelikonen. För att hitta de seriella portarna, referera till den bild som visar data portar på baksidan av din enhet.
2. Identifiera den aktiva kontrollenheten på din enhet-bakplan. En blinkande blå LED indikerar att kontrollanten är aktiv.
3. Använd den angivna seriella kabeln (vid behov, USB-seriell konverterare för din bärbara dator) och Anslut konsolen eller dator (med terminalemulering till enheten) till den seriella porten på den aktiva kontrollenheten.
4. Installera seriell USB-drivrutiner (medföljer enheten) på datorn.
5. Konfigurera seriell anslutning enligt följande:
   
   * 115 200 bit/s
   * 8 databitar
   * 1 stop-bitars
   * Ingen paritet
   * Flödeskontroll inställd **None**
6. Kontrollera att anslutningen fungerar genom att trycka på RETUR på konsolen. Menyn för seriekonsolen bör visas.

> [!NOTE]
> **Lights-Out-hantering:** när enheten installeras i ett fjärranslutet datacenter eller i ett rum för datorn med begränsad åtkomst, kontrollera att de seriella anslutningarna till båda styrenheterna alltid är anslutna till en seriell konsol växel eller liknande utrustning. Detta tillåter fjärrstyrning för out-of-band- och stöd för åtgärder vid avbrott i nätverket eller oväntade fel.
> 
> 

Du har slutfört kablar enheten till ström, nätverksåtkomst och seriell anslutning. Nästa steg är att konfigurera programvaran på din enhet.

## <a name="next-steps"></a>Nästa steg
Du är nu redo att [distribuera och konfigurera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

