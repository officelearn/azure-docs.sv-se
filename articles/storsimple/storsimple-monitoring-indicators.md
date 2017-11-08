---
title: "StorSimple övervakning indikatorer | Microsoft Docs"
description: "Beskriver lysdioder (led) och akustiska larm som används för att övervaka status för StorSimple-enhet."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 9ae0caec211dc1199f0abd2ce9bc0c7ad11c02ec
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Använda StorSimple övervakning indikatorer för att hantera din enhet


## <a name="overview"></a>Översikt
StorSimple-enheten inkluderar lysdioder (led) och automatlarm som du kan använda för att övervaka moduler och övergripande status för StorSimple-enhet. Övervakning indikatorer kan hittas på maskinvarukomponenter för enhetens primära höljet och EBOD höljet. Övervakning indikatorer kan vara indikatorer eller hörbart larm.

Det finns tre Indikator tillstånd används för att visa status för en modul: grön blinkande grönt till rött gul eller röd gul.  

* Grönt indikatorer representerar en operativ hälsostatus.  
* Blinkande grönt till rött gul indikatorer representerar förekomsten av icke-kritiska villkor som kan kräva åtgärder från användaren.  
* Röd gul indikatorer betyda att det finns ett allvarligt fel som finns i modulen.  

Resten av den här artikeln beskriver de olika övervakning indikator led, deras platser på StorSimple-enhet, enhetens status baserat på Indikator tillstånd och alla associerade hörbart larm.

## <a name="front-panel-indicator-leds"></a>Frontpanel indikator indikatorer
Framför panelen kallas även den *operations panelen* eller *ops panelen*, visar den sammanlagd statusen för alla moduler i systemet. Frontpanel är identiska på den primära StorSimple och EBOD höljet och visas nedan.  

   ![Enheten frontpanel][1]

Frontpanel innehåller följande faktorer:  

1. Ljud av
2. Power indikator Indikator (grönt/Röd-gul)
3. Modulen fel indikatorns LEDDE (på OFF-Röd-gul)
4. Logiska fel indikatorns LEDDE (på OFF-Röd-gul
5. Enhet ID visas  

Den största skillnaden mellan frontpanel indikatorer för enheten och de för höljet EBOD är den **System enhetsnummer identifiering** visas på skärmen Indikator. ID: T som visas på enheten är standardenheten **00**, medan standard enhets-ID visas på höljet EBOD är **01**. På så sätt kan du snabbt skilja mellan enheten och EBOD höljet när enheten är påslagen. Om enheten är inaktiverad, använder du informationen i [aktiverar en ny enhet](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) att skilja enheten från EBOD höljet.  

## <a name="front-panel-led-status"></a>Frontpanel Indikator status
Använd följande tabell för att identifiera status visas med indikatorer på panelen för enheten eller EBOD höljet.  

| System power | Modul-fel | Logiska fel | Larm | Status |
| --- | --- | --- | --- | --- |
| Röd gul |INAKTIVERA |INAKTIVERA |Saknas |AC power förlorad, arbetar på säkerhetskopiering power eller AC power och domänkontrollant moduler har tagits bort. |
| Grön |ON |ON |Saknas |Testa tillstånd för OPS panelen slå på strömmen (5-tal) |
| Grön |INAKTIVERA |INAKTIVERA |Saknas |Slå på alla funktioner som är bra |
| Grön |ON |Saknas |PCM fel led, fläkt fel indikatorer |PCM begåtts, fläkt fel, över eller under temperatur |
| Grön |ON |Saknas |I/o-modulen indikatorer |Modulen begåtts domänkontrollant |
| Grön |ON |Saknas |Saknas |Höljet logik-fel |
| Grön |Flash |Saknas |Modulstatus Indikator för domänkontrollant modul. PCM fel led, fläkt fel indikatorer |Okänd controller modultypen installerat I2C bus fel, konfigurationsfel för domänkontrollant modulen vital product data (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power kylning modul (PCM) indikator indikatorer
Power kylning modul (PCM) indikator indikatorer finns på baksidan av den primära hölje eller EBOD höljet på varje PCM-modul. Det här avsnittet beskrivs hur du använder följande indikatorer för att övervaka status för din StorSimple-enhet.  

* PCM indikatorer för primära höljet
* PCM indikatorer för höljet EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM indikatorer för primära höljet
StorSimple-enhet har en 764W PCM modul med en extra batteri. Följande bild visar panelen Indikator för enheten.  

   ![PCM indikatorer på primära höljet][2]

Indikator för förklaringen:

1. AC strömavbrott
2. Fläkt fel
3. Batteri fel
4. PCM OK
5. DC-fel
6. Bra batteri  

Status för PCM anges på panelen Indikator. Panelen enheten PCM Indikator har sex indikatorer. Fyra av dessa indikatorer visar status för strömförsörjningen och fläkten. De återstående två indikatorer visar status för säkerhetskopiering batterimodulen i PCM. Du kan använda följande tabeller för att avgöra status för PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM indikator indikatorer för strömförsörjning och -fläkt
| Status | PCM bra (grönt) | AC misslyckas (gult) | Fläkt misslyckas (gult) | Domänkontrollanten misslyckas (gult) |
| --- | --- | --- | --- | --- |
| Ingen AC-ström (för att hölje) |INAKTIVERA |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| Ingen AC-ström (denna PCM endast) |INAKTIVERA |ON |INAKTIVERA |ON |
| AC finns på PCM - OK |ON |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| PCM misslyckas (fläkt misslyckas) |INAKTIVERA |INAKTIVERA |ON |Saknas |
| PCM-fel (via amp över spänning över aktuella) |INAKTIVERA |ON |ON |ON |
| PCM (fläkt utanför toleransen) |ON |INAKTIVERA |INAKTIVERA |ON |
| Standby-läge |Blinkande |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| PCM hämtning av inbyggd programvara |INAKTIVERA |Blinkande |Blinkande |Blinkande |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM indikator indikatorer för säkerhetskopiering batteri
| Status | Batteri bra (grönt) | Batteri fel (gul) |
| --- | --- | --- |
| Batteri finns inte |INAKTIVERA |INAKTIVERA |
| Batteri finns och debiteras |ON |INAKTIVERA |
| Batteri laddar eller underhåll avslutning |Blinkande |INAKTIVERA |
| Batteri ”soft” fel (återställa) |INAKTIVERA |Blinkande |
| Batteri ”hårt” fel (oåterkalleligt) |INAKTIVERA |ON |
| Batteri disarmed |Blinkande |INAKTIVERA |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM indikatorer för höljet EBOD
EBOD höljet har en 580 w vid PCM och inget ytterligare batteri. Panelen PCM för höljet EBOD har indikator indikatorer endast för strömförsörjningar och fläkten. Följande bild visar dessa indikatorer.

   ![PCM indikatorer på höljet EBOD][3] 

Du kan använda följande tabell för att avgöra status för PCM.  

| Status | PCM bra (grönt) | AC misslyckas (gult) | Fläkt misslyckas (gult) | Domänkontrollanten misslyckas (gult) |
| --- | --- | --- | --- | --- |
| Ingen AC-ström (för att hölje) |INAKTIVERA |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| Ingen AC-ström (denna PCM endast) |INAKTIVERA |ON |INAKTIVERA |ON |
| AC presentera PCM ON – OK |ON |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| PCM misslyckas (fläkt misslyckas) |INAKTIVERA |INAKTIVERA |ON |X |
| PCM-fel (via amp över spänning över aktuella |INAKTIVERA |ON |ON |ON |
| PCM (fläkt utanför toleransen) |ON |INAKTIVERA |INAKTIVERA |ON |
| Vänteläge modellen |Blinkande |INAKTIVERA |INAKTIVERA |INAKTIVERA |
| PCM hämtning av inbyggd programvara |INAKTIVERA |Blinkande |Blinkande |Blinkande |

## <a name="controller-module-indicator-leds"></a>Domänkontrollanten modulen indikator indikatorer
StorSimple-enheten innehåller indikatorer för den primära domänkontrollanten och EBOD domänkontrollant moduler.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Övervakning indikatorer för den primära domänkontrollanten
Följande bild kan du identifiera indikatorer på den primära domänkontrollanten. (Alla komponenter som visas för att underlätta orientering.)  

   ![Övervaka led - primär domänkontrollant][4]

Använd följande tabell för att avgöra om modulen domänkontrollanten fungerar korrekt.  

### <a name="controller-indicator-leds"></a>Domänkontrollanten indikator indikatorer
| INDIKATOR | Beskrivning |
| --- | --- |
| Indikator för ID (blå) |Anger att modulen som ska identifieras. Om blå Indikator blinkar på en domänkontrollant som körs på domänkontrollanten är den aktiva styrenheten och den andra är i vänteläge controller. Mer information finns i [identifiera den aktiva styrenheten på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fel Indikator (gul) |Anger om ett fel på Kontrollpanelen. |
| OK Indikator (grön) |Konstant grönt anger att kontrollanten är OK. Blinkande grönt anger en domänkontrollant VPD konfigurationsfel. |
| SAS-aktivitet indikatorer (grön) |Konstant grönt anger en anslutning utan aktuell aktivitet. Blinkande grönt anger att anslutningen har en pågående aktivitet. |
| Status för Ethernet-indikatorer |Höger anger länken/nätverksaktivitet: (konstant grön) länken active (blinkande grönt) nätverksaktivitet. Vänster anger nätverkshastigheten: (gul) 1000 Mb/s och (grön) 100 Mb/s (OFF) 10 Mb/s. Beroende på komponenten modellen, kan den här ljus blinkar även om nätverkskortet inte har aktiverats. |
| EFTER indikatorer |Anger förloppet start när styrenheten är aktiverat. Om StorSimple-enheten inte kan starta är hjälper denna Indikator Microsoft-supporten identifiera punkten i startprocessen då felet inträffade. |

> [!IMPORTANT]
> Om felet Indikator upplysta, finns ett problem med modulen domänkontrollanten som kan lösas genom att starta om registeransvarige. Kontakta Microsoft Support om omstart av domänkontrollanten inte löser problemet.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Övervakning indikatorer för EBOD (EBOD hölje)
Varje 6 Gbit/s SAS EBOD domänkontrollanter har indikatorer som anger dess status enligt följande bild.  

  ![Övervaka led - EBOD hölje][5]

Använd följande tabell för att avgöra om modulen EBOD domänkontrollanten fungerar normalt.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD domänkontrollant modulen indikator indikatorer
| Status | I/o-modulen bra (grönt) | Fel i/o-modul (gul) | Värdaktivitet port (grön) |
| --- | --- | --- | --- |
| Domänkontrollanten modulen OK |ON |INAKTIVERA |- |
| Domänkontrollanten modulen fel |INAKTIVERA |ON |- |
| Ingen extern värd portanslutning |- |- |INAKTIVERA |
| Port för externa värdanslutning – ingen aktivitet |- |- |ON |
| Extern värd anslutningsport - aktivitet |- |- |Blinkande |
| Metadata för domänkontrollanten modulfel |Blinkande |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Diskenhet indikator indikatorer för primära höljet och EBOD hölje
StorSimple-enheten har diskenheter som finns i både primära höljet och EBOD höljet. Varje diskenhet innehåller övervakning indikator led, enligt beskrivningen i det här avsnittet. 

För diskenheterna visas status för enheten med en grön Indikator och en röd gul Indikator monteras på varje enhet operatör modul framför. Följande bild visar dessa indikatorer.

  ![Diskenhet indikatorer][6]

Använd följande tabell för att avgöra tillståndet för varje diskenhet, vilket i sin tur påverkar den övergripande frontpanelen Indikator status.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Diskenhet indikator indikatorer för höljet EBOD
| Status | Aktiviteten OK Indikator (grön) | Fel Indikator (röd gul) | Associerade ops panelen Indikator |
| --- | --- | --- | --- |
| Ingen installerad enhet |INAKTIVERA |INAKTIVERA |Ingen |
| Enheten installerat och fungerar |Blinkar på/av aktivitet |X |Ingen |
| SCSI Enclosure Services (se) enhetsuppsättning identitet |ON |Blinkande 1 sekund på-1 sekund av |Ingen |
| Se enhetsuppsättning fel bitar |ON |ON |Logiska fel (röd) |
| Strömavbrott kontrollen krets |INAKTIVERA |ON |Modul-fel (röd) |

## <a name="audible-alarms"></a>Akustiska signaler
En StorSimple-enhet innehåller akustiska signaler som är associerade med både primära höljet och EBOD höljet. Ett akustiskt larm finns på frontpanel (även kallat panelen ops) både höljen. Att ett larm anger när det finns ett fel. Larm aktiveras för följande villkor:  

* Fläkt fel eller fel
* Spänning utanför intervallet
* Under eller över temperatur villkor
* Termisk överskridande
* Fel
* Logiska fel
* Ange Strömfel
* Borttagning av en exponent kylning modul (PCM)  

I följande tabell beskrivs de olika lägena för larm.  

### <a name="alarm-states"></a>Larm tillstånd
| Larm tillstånd | Åtgärd | Åtgärden med ljud av knappen trycks ned |
| --- | --- | --- |
| S0 |Normalt läge: tyst |Signal två gånger |
| S1 |Fel-läge: 1 sekund på-1 sekund av |Övergång till S2 eller S3 (se kommentarer) |
| S2 |Påminn läge: återkommande signal |Ingen |
| S3 |Ljudet är avstängt läge: tyst |Ingen |
| S4 |Kritiska fel läge: kontinuerlig larm |Inte tillgänglig: tyst läge inte aktiv |

> [!NOTE]
> * Tillståndet larm S1, om du inte trycker tyst inom 2 minuter övergår tillståndet automatiskt till S2 och S3.  
> * Larm tillstånd S1 till S4 tillbaka till S0 när villkoret fel är avmarkerad.  
> * Kritiska fel tillstånd S4 kan anges från andra tillstånd.  


Du kan stänga av att ett larm genom att trycka på knappen Ljud av på panelen ops. Stänga av automatisk ljudet utförs efter två minuter om växeln tyst inte drivs manuellt. När larmet är avstängt, fortsätter den att ljud med kort återkommande PIP att indikera att en problemet fortfarande kvarstår. Larm blir tyst när alla problem är avmarkerad.

I följande tabell beskrivs de olika larm villkor.

### <a name="alarm-conditions"></a>Larm villkor
| Status | Allvarsgrad | Larm | OPS panelen Indikator |
| --- | --- | --- | --- |
| PCM aviseringen – strömavbrott DC från en enda PCM |Fel – utan förlust av redundans |S1 |Modul-fel |
| PCM aviseringen – strömavbrott DC från en enda PCM |Fel – förlust av redundans |S1 |Modul-fel |
| PCM-fläkt misslyckas |Fel – förlust av redundans |S1 |Modul-fel |
| Flesta SBB-modulen upptäckte PCM-fel |Fel |S1 |Modul-fel |
| PCM tas bort |Konfigurationsfel |Ingen |Modul-fel |
| Höljet konfigurationsfel |Fel – kritisk |S1 |Modul-fel |
| Låg temperatur-varning |Varning |S1 |Modul-fel |
| Hög temperatur-varning |Varning |S1 |Modul-fel |
| Över temperatur larm |Fel – kritisk |S1 |Modul-fel |
| I2C bus fel |Fel – förlust av redundans |S1 |Modul-fel |
| OPS panelen kommunikationsfel (I2C) |Fel – kritisk |S1 |Modul-fel |
| Domänkontrollanten fel |Fel – kritisk |S1 |Modul-fel |
| Flesta SBB gränssnittet modulen fel |Fel – kritisk |S1 |Modul-fel |
| Flesta SBB gränssnittet modulen fel – ingen fungerande moduler återstående |Fel – kritisk |S4 |Modul-fel |
| Flesta SBB gränssnittet modulen tas bort |Varning |Ingen |Modul-fel |
| Enheten Strömfel kontroll |Varning – ingen strömavbrott enhet |S1 |Modul-fel |
| Enheten Strömfel kontroll |Fel – kritiska; strömavbrott enhet |S1 |Modul-fel |
| Enheten tas bort |Varning |Ingen |Modul-fel |
| Otillräckligt med ström tillgänglig |Varning |Ingen |Modul-fel |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [StorSimple status och maskinvarukomponenter](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


