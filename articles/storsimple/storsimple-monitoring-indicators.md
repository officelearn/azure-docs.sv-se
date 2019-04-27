---
title: StorSimple övervakningsindikatorer | Microsoft Docs
description: 'Beskriver lysdioder (led: ar) och akustiska signaler som används för att övervaka status för StorSimple-enheten.'
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 59dee7b9-ca6d-4fd9-96e6-a0071e8d248e
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: ef8acf1c3c9211168ebacc8d62647f6789c745a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630656"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Använda StorSimple övervakningsindikatorer för att hantera din enhet


## <a name="overview"></a>Översikt
StorSimple-enheten inkluderar lysdioder (led: ar) och larm som du kan använda för att övervaka moduler och övergripande status för StorSimple-enheten. Övervakningsindikatorer kan hittas på maskinvarukomponenter för enhetens primära hölje och EBOD-kabinett. Övervakningsindikatorer kan vara antingen led: ar eller akustiska signaler.

Det finns tre LED tillstånd används för att ange status för en modul: grönt, blinkar grön till röd-gul eller röd-gul.  

* Grön-led: ar representerar en operativ hälsostatus.  
* Blinkande grön till röd-gul led: ar representerar förekomsten av icke-kritiska villkor som kan kräva åtgärder från användaren.  
* Röd-gul-led: ar betyda att det finns ett kritiskt fel finns i modulen.  

Resten av den här artikeln beskriver de olika av övervakningsindikatorer, deras platser på StorSimple-enhet, enhetens status baserat på distributionens LED och alla associerade akustiska signaler.

## <a name="front-panel-indicator-leds"></a>Frontpanel övervakningsindikatorer
Framför panelen, även kallat den *operations panelen* eller *ops panelen*, visar sammanställd status för alla moduler i systemet. Frontpanel är identiska på den primära StorSimple och EBOD-höljet och visas nedan.  

   ![Enheten frontpanel][1]

Frontpanel innehåller följande faktorer:  

1. Ljud av
2. Power-indikator LED (grön/röd-gul)
3. Modulen fel indikator LEDDE (på Röd-gul/OFF)
4. Logiska fel indikator LEDDE (på Röd-gul/av
5. Visa för enhet-ID  

Den stora skillnaden mellan frontpanel-led: ar för enheten och de för EBOD-hölje är den **System enhet identifieringsnummer** visas på LED-skärmen. Standardenhet-ID som visas på enheten är **00**, medan standard enhets-ID visas på EBOD-hölje är **01**. På så sätt kan du snabbt skilja mellan enheten och EBOD-kabinett när enheten är påslagen. Om enheten är avstängd, kan du använda informationen i [aktivera en ny enhet](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) att skilja enheten från EBOD-höljet.  

## <a name="front-panel-led-status"></a>Frontpanel LED status
Använd följande tabell för att identifiera den status som visas med led: ar på panelen för enheten eller EBOD-höljet.  

| System power | Modulen fel | Logiska fel | Larm | Status |
| --- | --- | --- | --- | --- |
| Red-amber |AV |AV |Gäller inte |Nätström tappas bort, körs på strömtillförsel eller nätström på och kontrollanten moduler har tagits bort. |
| Grön |ON |ON |Gäller inte |OPS panelen ström på 5 (sek) testa tillstånd |
| Grön |AV |AV |Gäller inte |Ström på alla funktioner som är bra |
| Grön |ON |Gäller inte |PCM fel led: ar, fläkt fel led: ar |Alla PCM-fel, fläkt fel, över eller under temperatur |
| Grön |ON |Gäller inte |I/o-modulen led: ar |Alla domänkontrollanter modulen fel |
| Grön |ON |Gäller inte |Gäller inte |Hölje logic-fel |
| Grön |Flash |Gäller inte |Modulstatus LEDDE på controller-modulen. PCM fel led: ar, fläkt fel led: ar |Okänd controller Modultyp installerat I2C bus fel, konfigurationsfel för domänkontrollant modulen vital product data (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power kylning modulen (PCM) övervakningsindikatorer
Power kylning modulen (PCM) övervakningsindikatorer finns på baksidan av den primära hölje eller EBOD hölje på varje PCM-modul. Det här avsnittet beskrivs hur du använder följande led: ar för att övervaka status för din StorSimple-enhet.  

* PCM-led: ar för det primära höljet
* PCM-led: ar för EBOD-hölje

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-led: ar för det primära höljet
StorSimple-enheten har en 764W PCM modul med en extra batteri. Följande bild visar panelen Indikator för enheten.  

   ![PCM-led: ar på det primära höljet][2]

LED förklaring:

1. AC-strömavbrott
2. Fläkt fel
3. Batteri fel
4. PCM OK
5. DC-fel
6. Bra batteri  

Status för PCM visas på panelen LED. Panelen enheten PCM LED har sex led: ar. Fyra av dessa led: ar visa status för strömförsörjningen och fläkten. De återstående två led: ar Ange status för modulen extrabatteri i PCM. Du kan använda följande tabeller för att bestämma tillståndet hos PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM övervakningsindikatorer för strömförsörjning och -fläkt
| Status | PCM bra (grönt) | AC misslyckas (gult) | Fläkt misslyckas (gult) | DC misslyckas (gult) |
| --- | --- | --- | --- | --- |
| Inga nätström (till enhet) |AV |AV |AV |AV |
| Inga nätström (endast den här PCM) |AV |ON |AV |ON |
| AC presentera PCM-på - OK |ON |AV |AV |AV |
| PCM misslyckas (fläkt misslyckas) |AV |AV |ON |Gäller inte |
| PCM-fel (över amp över spänning över aktuella) |AV |ON |ON |ON |
| PCM (fläkt utanför tolerans) |ON |AV |AV |ON |
| Läget Snabb växling |Blinkande |AV |AV |AV |
| PCM nedladdning |AV |Blinkande |Blinkande |Blinkande |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM övervakningsindikatorer för reservbatteriet
| Status | Batteri bra (grönt) | Batteri fel (gul) |
| --- | --- | --- |
| Batteri finns inte |AV |AV |
| Batteri finns och debiteras |ON |AV |
| Batteri debitera eller underhåll utsläpp |Blinkande |AV |
| Batteri ”soft” fel (återställa) |AV |Blinkande |
| Batteri ”hårt” fel (oåterkalleligt) |AV |ON |
| Batteri disarmed |Blinkande |AV |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM-led: ar för EBOD-hölje
EBOD-höljet har en 580 w vid PCM och inga ytterligare batteri. Panelen PCM för EBOD-höljet har övervakningsindikatorer bara för strömförsörjningar och fläkten. Följande bild visar dessa led: ar.

   ![PCM-led: ar på EBOD-hölje][3] 

Du kan använda tabellen nedan för att bestämma tillståndet hos PCM.  

| Status | PCM bra (grönt) | AC misslyckas (gult) | Fläkt misslyckas (gult) | DC misslyckas (gult) |
| --- | --- | --- | --- | --- |
| Inga nätström (till enhet) |AV |AV |AV |AV |
| Inga nätström (endast den här PCM) |AV |ON |AV |ON |
| AC presentera PCM vidare – OK |ON |AV |AV |AV |
| PCM misslyckas (fläkt misslyckas) |AV |AV |ON |X |
| PCM-fel (över amp över spänning över aktuella |AV |ON |ON |ON |
| PCM (fläkt utanför tolerans) |ON |AV |AV |ON |
| Standby-modellen |Blinkande |AV |AV |AV |
| PCM nedladdning |AV |Blinkande |Blinkande |Blinkande |

## <a name="controller-module-indicator-leds"></a>Controller modulen övervakningsindikatorer
StorSimple-enheten innehåller led: ar för den primära kontrollenheten och EBOD controller moduler.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Övervakning led: ar för primär kontrollenhet
Följande bild kan du identifiera led: ar på den primära kontrollenheten. (Alla komponenter som är registrerade för att underlätta orientering.)  

   ![Övervaka led: ar – primär kontrollenhet][4]

Använd följande tabell för att avgöra om modulen domänkontrollanten fungerar korrekt.  

### <a name="controller-indicator-leds"></a>Controller övervakningsindikatorer
| LED | Beskrivning |
| --- | --- |
| ID-LED (blå) |Anger att modulen som identifieras. Om den blå LED blinkar på en domänkontrollant som körs, kontrollanten är den aktiva kontrollenheten och är kontrollenheten i vänteläge. Mer information finns i [identifiera den aktiva kontrollanten på din enhet](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fel LED (gul) |Anger om ett fel uppstår på Kontrollpanelen. |
| OK LED (grönt) |Stadig grönt anger att kontrollanten är OK. Blinkande grönt anger en kontrollant VPD konfigurationsfel. |
| SAS-aktivitet led: ar (grönt) |Stadig grönt anger en anslutning med ingen aktuell aktivitet. Blinkande grönt anger att anslutningen har pågående aktivitet. |
| Status för Ethernet-led: ar |Höger anger länken/nätverksaktivitet: (stadig grönt) länk är aktiv (blinkar grönt) nätverksaktivitet. Vänster anger nätverkshastigheten: (gul) 1000 Mb/s och (grön) 100 Mb/s (OFF) 10 Mb/s. Beroende på komponentmodellen, kan den här ljus blinkar även om nätverksgränssnittet inte har aktiverats. |
| POST-led: ar |Anger start förloppet när kontrollanten är påslagen. Om det inte går att starta StorSimple-enheten, kan den här LED Microsoft Support identifiera punkten i startprocessen då felet inträffade. |

> [!IMPORTANT]
> Om felet LED lyser finns det ett problem med modulen controller som kan lösas genom att starta om styrenheten. Kontakta Microsoft Support om du startar om kontrollanten inte löser problemet.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Övervakning led: ar för EBOD (EBOD hölje)
Varje 6 Gb/s SAS EBOD-domänkontrollanter har led: ar som anger dess status enligt följande bild.  

  ![Övervaka led: ar - EBOD hölje][5]

Använd följande tabell för att avgöra om modulen EBOD domänkontrollanten fungerar normalt.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD controller modulen övervakningsindikatorer
| Status | I/o-modulen bra (grönt) | Fel i/o-modul (gul) | Värdaktivitet port (grönt) |
| --- | --- | --- | --- |
| Controller modulen OK |ON |AV |- |
| Controller modulen fel |AV |ON |- |
| Ingen extern värd port-anslutning |- |- |AV |
| Port för externa värdanslutning – ingen aktivitet |- |- |ON |
| Port för externa värdanslutning - aktivitet |- |- |Blinkande |
| Metadata för domänkontrollanten modulfel |Blinkande |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Övervakningsindikatorer diskenhet för primär lagringsenhet och EBOD hölje
StorSimple-enheten har diskenheter som finns i både primära höljet och EBOD-kabinett. Varje diskenhet innehåller övervakning övervakningsindikatorer, enligt beskrivningen i det här avsnittet. 

För diskenheter, visas status för enheten med en grön Indikator och en röd-gul-LED monteras på varje enhet operatör modul framför. Följande bild visar dessa led: ar.

  ![Diskenhet led: ar][6]

Använd följande tabell för att bestämma tillståndet för varje diskenhet som i sin tur påverkar den totala frontpanel LED status.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Övervakningsindikatorer diskenhet för EBOD-hölje
| Status | Aktivitet OK LED (grönt) | Fel LED (röd-gul) | Associerade ops panelen Indikator |
| --- | --- | --- | --- |
| Ingen installerad enhet |AV |AV |Ingen |
| Enheten installerat och fungerar |Blinkar på/av med aktivitet |X |Ingen |
| Identitetsuppsättningen för SCSI Enclosure Services (SES) enheten |ON |Blinkande 1 sekund på/1 sekund av |Ingen |
| SES enhetsuppsättning fault-bitars |ON |ON |Logiska fel (röd) |
| Strömavbrott kontroll krets |AV |ON |Modulen fel (röd) |

## <a name="audible-alarms"></a>Akustiska signaler
En StorSimple-enhet innehåller akustiska signaler som är associerad med både primära höljet och EBOD-kabinett. En ljudligt alarm finns på frontpanel (även kallat panelen ops) för både höljen. Ljudligt alarm anger när ett fel finns. Följande villkor aktiveras larmet:  

* Fläkt fel eller fel
* Voltage utanför intervallet
* Över eller under temperatur villkor
* Termisk överskridande
* Fel
* Logiska fel
* Ange Strömfel
* Borttagning av en exponent kylning modulen (PCM)  

I följande tabell beskrivs de olika lägena för larmet.  

### <a name="alarm-states"></a>Larm tillstånd
| Larm tillstånd | Åtgärd | Åtgärden med ljud av tryckt |
| --- | --- | --- |
| S0 |Normalt läge: tyst |Två gånger ljudsignal |
| S1 |Fel-läge: 1 sekund/1 sekund av |Övergången till S2 eller S3 (se kommentarer) |
| S2 |Påminn läge: tillfälliga ljudsignal |Ingen |
| S3 |Ljudet är avstängt läge: tyst |Ingen |
| S4 |Kritiska fel läge: kontinuerlig larm |Inte tillgänglig: slå på inte aktiv |

> [!NOTE]
> * Larm tillståndet S1, om du inte trycker ljud av inom två minuter övergår tillståndet automatiskt till S2 eller S3.  
> * Larm tillstånd S1 till S4 tillbaka till S0 när villkoret fel är avmarkerad.  
> * Kritiska fel tillstånd S4 kan anges från andra tillstånd.  


Du kan stänga av ljudligt alarm genom att trycka på knappen Ljud av på panelen ops. Stänga av automatisk ljudet uppstår efter två minuter om växeln ljud av inte drivs manuellt. När larmet är avstängt, fortsätter den att ljud med kort tillfälliga signaler som visar att det fortfarande finns ett problem. Larmet blir tyst när alla problem är avmarkerad.

I följande tabell beskrivs de olika larm-villkor.

### <a name="alarm-conditions"></a>Larm villkor
| Status | Severity | Larm | OPS panelen Indikator |
| --- | --- | --- | --- |
| PCM avisering – strömavbrott DC från en enda PCM |Fel – ingen förlust av redundans |S1 |Modulen fel |
| PCM avisering – strömavbrott DC från en enda PCM |Fel – förlust av redundans |S1 |Modulen fel |
| PCM-fläkt misslyckas |Fel – förlust av redundans |S1 |Modulen fel |
| Flesta SBB-modul upptäckte att PCM-fel |Fel |S1 |Modulen fel |
| PCM som har tagits bort |Konfigurationsfel |Ingen |Modulen fel |
| Konfigurationsfel för hölje |Fel – kritisk |S1 |Modulen fel |
| Låg temperatur-varning |Varning |S1 |Modulen fel |
| Hög temperatur-varning |Varning |S1 |Modulen fel |
| Över temperatur larm |Fel – kritisk |S1 |Modulen fel |
| I2C bus fel |Fel – förlust av redundans |S1 |Modulen fel |
| OPS panelen kommunikationsfel (I2C) |Fel – kritisk |S1 |Modulen fel |
| Controller-fel |Fel – kritisk |S1 |Modulen fel |
| Flesta SBB gränssnittet modulen fel |Fel – kritisk |S1 |Modulen fel |
| Flesta SBB gränssnittet modulen fel – ingen fungerande moduler som återstår |Fel – kritisk |S4 |Modulen fel |
| Flesta SBB gränssnittet modulen tas bort |Varning |Ingen |Modulen fel |
| Enheten Strömfel kontroll |Varning – inga strömavbrott enhet |S1 |Modulen fel |
| Enheten Strömfel kontroll |Fel – kritiska; strömavbrott enhet |S1 |Modulen fel |
| Enheten tas bort |Varning |Ingen |Modulen fel |
| Otillräckligt med ström som är tillgängliga |Varning |inga |Modulen fel |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


