---
title: StorSimple övervakningsindikatorer | Microsoft-dokument
description: Beskriver de lysdioder (lysdioder) och hörbara larm som används för att övervaka StorSimple-enhetens status.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60630656"
---
# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Använd StorSimple-övervakningsindikatorer för att hantera din enhet


## <a name="overview"></a>Översikt
Din StorSimple-enhet innehåller lysdioder (lysdioder) och larm som du kan använda för att övervaka Modulerna och storsimple-enhetens övergripande status. Övervakningsindikatorerna finns på maskinvarukomponenterna i enhetens primära kapsling och EBOD-kapslingen. Övervakningsindikatorerna kan vara antingen lysdioder eller hörbara larm.

Det finns tre LED-lägen som används för att ange status för en modul: grön, blinkande grön till röd-gul, eller röd-gul.  

* Gröna lysdioder representerar en felfri driftsstatus.  
* Blinkande grönt till röd-gult lysdioder representerar förekomsten av icke-kritiska förhållanden som kan kräva åtgärder från användaren.  
* Röd-gula lysdioder indikerar att det finns ett kritiskt fel i modulen.  

Resten av den här artikeln beskriver de olika övervakningsindikatorlysdioderna, deras platser på StorSimple-enheten, enhetens status baserat på LED-tillstånd och eventuella tillhörande hörbara larm.

## <a name="front-panel-indicator-leds"></a>Indikatorlysdioder på frontpanelen
Frontpanelen, även känd som *operationspanelen* eller *ops-panelen,* visar den sammanlagda statusen för alla moduler i systemet. Frontpanelen är identisk på StorSimple-primärt och EBOD-kapslingen och illustreras nedan.  

   ![Enhetens frontpanel][1]

Frontpanelen innehåller följande indikatorer:  

1. Ljudavstängning
2. Strömindikator LED (grön/röd-gulfärgad)
3. Modulfelindikator LED (PÅ röd-gul/AV)
4. LyolystindikatorLAMPA (PÅ röd-gul/AV
5. Enhets-ID-skärm  

Den största skillnaden mellan frontpanelens lysdioder för enheten och de för EBOD-höljet är **systemenhetens identifieringsnummer** som visas på LED-displayen. Standardenhets-ID som visas på enheten är **00**, medan standardenhets-ID som visas på EBOD-höljet är **01**. På så sätt kan du snabbt skilja mellan enheten och EBOD-höljet när enheten är påslagen. Om enheten är avstängd använder du informationen i [Aktivera en ny enhet](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) för att skilja enheten från EBOD-höljet.  

## <a name="front-panel-led-status"></a>LED-status på frontpanelen
Använd följande tabell för att identifiera den status som anges av lysdioderna på frontpanelen för enheten eller EBOD-höljet.  

| Systemström | Modul fel | Logiskt fel | Larm | Status |
| --- | --- | --- | --- | --- |
| Röd-gul |OFF |OFF |Ej tillämpligt |Strömström som gått förlorad, drift på reservkraft eller ström på och styrenhetsmodulerna togs bort. |
| Grön |ON |ON |Ej tillämpligt |Ops-panel påslagningstillstånd (5s) |
| Grön |OFF |OFF |Ej tillämpligt |Slå på, alla funktioner bra |
| Grön |ON |Ej tillämpligt |PCM-fel lysdioder, fläktfel lysdioder |Alla PCM-fel, fläktfel, över eller under temperatur |
| Grön |ON |Ej tillämpligt |I/O-modullysdioder |Fel på alla styrenhetsmoduler |
| Grön |ON |Ej tillämpligt |Ej tillämpligt |Fel på höljelogik |
| Grön |Flash |Ej tillämpligt |Modulstatus LED på styrenhetsmodul. PCM-fel lysdioder, fläktfel lysdioder |Okänd styrenhetsmodultyp installerad, I2C-bussfel, styrenhetsmodulskonfigurationsfel för viktiga produktdata (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Pcm-indikatorlysdioder (Power Cooling Module)
PCM-indikatorlysdioder (Power Cooling Module) finns på baksidan av det primära höljet eller EBOD-höljet på varje PCM-modul. I det här avsnittet beskrivs hur du använder följande lysdioder för att övervaka statusen för din StorSimple-enhet.  

* PCM-lysdioder för primärhöljet
* PCM-lysdioder för EBOD-höljet

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM-lysdioder för primärhöljet
StorSimple-enheten har en 764W PCM-modul med ett extra batteri. Följande bild visar LED-panelen för enheten.  

   ![PCM-lysdioder på det primära höljet][2]

LED-förklaring:

1. Strömavbrott
2. Fläktfel
3. Batterifel
4. PCM OK
5. DC-fel
6. Batteriet bra  

PCM:s status anges på LED-panelen. Enheten PCM LED-panel har sex lysdioder. Fyra av dessa lysdioder visar strömförsörjningens och fläktens status. De återstående två lysdioderna anger status för reservbatterimodulen i PCM. Du kan använda följande tabeller för att fastställa pcm-statusen.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM-indikatorlysdioder för strömförsörjning och fläkt
| Status | PCM OK (grön) | AC-fel (gult) | Fläkten misslyckas (gul) | DC misslyckas (gul) |
| --- | --- | --- | --- | --- |
| Ingen växelström (till hölje) |OFF |OFF |OFF |OFF |
| Ingen växelström (endast denna PCM) |OFF |ON |OFF |ON |
| AC nuvarande PCM ON - OK |ON |OFF |OFF |OFF |
| PCM misslyckas (fläkten misslyckas) |OFF |OFF |ON |Ej tillämpligt |
| PCM-fel (över förstärkare, överspänning, över ström) |OFF |ON |ON |ON |
| PCM (fläkt av tolerans) |ON |OFF |OFF |ON |
| Passningsläge |Blinkande |OFF |OFF |OFF |
| PCM firmware nedladdning |OFF |Blinkande |Blinkande |Blinkande |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM-indikatorlysdioder för reservbatteriet
| Status | Batteriet bra (grönt) | Batterifel (gult) |
| --- | --- | --- |
| Batteriet finns inte |OFF |OFF |
| Batteri som finns och laddas |ON |OFF |
| Laddning eller underhåll urladdning av batteriet |Blinkande |OFF |
| Batteri "mjuk" fel (återvinningsbar) |OFF |Blinkande |
| Batteri "hårt" fel (kan inte återvinnas) |OFF |ON |
| Batteriet avväpnas |Blinkande |OFF |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM-lysdioder för EBOD-höljet
EBOD-höljet har en 580W PCM och inget extra batteri. PCM-panelen för EBOD-höljet har endast indikatorlysdioder för nätaggregat och fläkt. Följande bild visar dessa lysdioder.

   ![PCM-lysdioder på EBOD-höljet][3] 

Du kan använda följande tabell för att fastställa pcm-statusen.  

| Status | PCM OK (grön) | AC-fel (gult) | Fläkten misslyckas (gul) | DC misslyckas (gul) |
| --- | --- | --- | --- | --- |
| Ingen växelström (till hölje) |OFF |OFF |OFF |OFF |
| Ingen växelström (endast denna PCM) |OFF |ON |OFF |ON |
| AC nuvarande PCM PÅ - OK |ON |OFF |OFF |OFF |
| PCM misslyckas (fläkten misslyckas) |OFF |OFF |ON |X |
| PCM-fel (överspänning, överspänning, överström |OFF |ON |ON |ON |
| PCM (fläkt av tolerans) |ON |OFF |OFF |ON |
| Standby-modell |Blinkande |OFF |OFF |OFF |
| PCM firmware nedladdning |OFF |Blinkande |Blinkande |Blinkande |

## <a name="controller-module-indicator-leds"></a>Indikatorlysdioder för styrenhetsmodul
StorSimple-enheten innehåller lysdioder för den primära styrenheten och EBOD-styrenheterna.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Övervakningslysdioder för den primära styrenheten
Följande bild hjälper dig att identifiera lysdioderna på den primära styrenheten. (Alla komponenter är listade för att underlätta orienteringen.)  

   ![Övervakningslysdioder - primär styrenhet][4]

Använd följande tabell för att avgöra om styrenhetsmodulen fungerar korrekt.  

### <a name="controller-indicator-leds"></a>Styrindikator lysdioder
| Ledde | Beskrivning |
| --- | --- |
| ID LED (blå) |Anger att modulen identifieras. Om den blå lysdioden blinkar på en löpstyrenhet är handkontrollen den aktiva handkontrollen och den andra är standby-styrenheten. Mer information finns i [Identifiera den aktiva handkontrollen på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Fellampa (gul) |Indikerar ett fel i handkontrollen. |
| OK LED (grön) |Stadigt grönt indikerar att handkontrollen är OK. Blinkande grönt indikerar ett VPD-konfigurationsfel för styrenheten. |
| SAS aktivitetslysdioder (grön) |Stadigt grönt indikerar en anslutning utan aktuell aktivitet. Blinkande grönt indikerar att anslutningen har pågående aktivitet. |
| Ethernet-status lysdioder |Höger sida anger länk/nätverksaktivitet: (stadig grön) länk aktiv, (blinkande grön) nätverksaktivitet. Vänster sida anger nätverkshastighet: (gul) 1000 Mb/s, (grön) 100 Mb/s och (OFF) 10 Mb/s. Beroende på komponentmodellen kan den här lampan blinka även om nätverksgränssnittet inte är aktiverat. |
| POST LYSDIODER |Anger startstatus när handkontrollen är påslagen. Om StorSimple-enheten inte startar hjälper den här lysdioden Microsoft Support att identifiera den punkt i startprocessen där felet uppstod. |

> [!IMPORTANT]
> Om fellampan lyser är det problem med styrenhetsmodulen som kan lösas genom att du startar om styrenheten. Kontakta Microsoft Support om det inte går att lösa problemet med att starta om handkontrollen.  
> 
> 

### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Övervakningslysdioder för EBOD (EBOD-kapsling)
Var och en av DE 6 GB/s SAS EBOD-styrenheterna har lysdioder som anger dess status enligt följande bild.  

  ![Övervakningslysdioder - EBOD-kapsling][5]

Använd följande tabell för att avgöra om EBOD-styrenheten fungerar normalt.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD controller modul indikator lysdioder
| Status | I/O-modul OK (grön) | I/O-modulfel (gult) | Värdportaktivitet (grön) |
| --- | --- | --- | --- |
| Controller modul OK |ON |OFF |- |
| Fel på styrenhetsmodul |OFF |ON |- |
| Ingen extern värdportanslutning |- |- |OFF |
| Extern värdportanslutning – ingen aktivitet |- |- |ON |
| Extern värdportanslutning - aktivitet |- |- |Blinkande |
| Metadatafel för kontrollmodul |Blinkande |- |- |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lysdioder för diskenhetsindikator för det primära höljet och EBOD-höljet
StorSimple-enheten har hårddiskar som finns i både den primära höljet och EBOD-höljet. Varje diskenhet innehåller övervakningsindikatorlysdioder, enligt beskrivningen i det här avsnittet. 

För diskenheterna indikeras enhetens status med en grön lysdiod och en röd-gul LED monterad på framsidan av varje drivhållaremodul. Följande bild visar dessa lysdioder.

  ![Lysdioder för diskenhet][6]

Använd följande tabell för att bestämma tillståndet för varje diskenhet, vilket i sin tur påverkar den övergripande led-statusen på frontpanelen.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Lysdioder för diskenhetsindikator för EBOD-höljet
| Status | Aktivitet OK LED (grön) | Fellysdiod (röd-gul) | Led-lampa för tillhörande ops-panel |
| --- | --- | --- | --- |
| Ingen enhet installerad |OFF |OFF |Inget |
| Enhet installerad och i drift |Blinkar på/av med aktivitet |X |Inget |
| SE-enhetsidentitetsuppsättning (SCSI Enclosure Services) |ON |Blinkande 1 sekund på / 1 sekund bort |Inget |
| SES-enhetsfelbituppsättning |ON |ON |Logiskt fel (rött) |
| Strömavbrottsbrytare |OFF |ON |Modulfel (röd) |

## <a name="audible-alarms"></a>Hörbara larm
En StorSimple-enhet innehåller hörbara larm som är kopplade till både den primära inneslutningen och EBOD-höljet. Ett hörbart larm finns på frontpanelen (även känd som ops-panelen) i båda kapslingarna. Det hörbara larmet indikerar när ett feltillstånd föreligger. Följande villkor aktiverar larmet:  

* Fläktfel eller fel
* Spänning utanför räckhåll
* Över eller under temperaturtillstånd
* Termisk överskridande
* Systemfel
* Logiskt fel
* Fel vid strömförsörjning
* Borttagning av en effektkylmodul (PCM)  

I följande tabell beskrivs de olika larmtillstånden.  

### <a name="alarm-states"></a>Larmtillstånd
| Larmtillstånd | Åtgärd | Åtgärden med knappen Ljud av är intryckt |
| --- | --- | --- |
| S0 |Normalt läge: tyst |Pip två gånger |
| S1 |Felläge: 1 sekund på/1 sekund av |Övergång till S2 eller S3 (se anteckningar) |
| S2 |Påminnläge: intermittent pip |Inget |
| S3 |Avstängt läge: tyst |Inget |
| S4 |Kritiskt felläge: kontinuerligt larm |Inte tillgängligt: ljud av inte aktiv |

> [!NOTE]
> * I larmtillstånd S1, om du inte trycker på mute inom 2 minuter, övergår tillståndet automatiskt till S2 eller S3.  
> * Larmet säger att S1 till S4 återgår till S0 efter att felet var åtgärdat.  
> * Kritiskt feltillstånd S4 kan anges från vilket annat tillstånd som helst.  


Du kan stänga av det hörbara larmet genom att trycka på ljudavstängningsknappen på ops-panelen. Automatisk avstängning sker efter två minuter om mute-brytaren inte manövreras manuellt. När alarmet är avstängt fortsätter det att ljuda med korta intermittenta pip för att indikera att det fortfarande finns ett problem. Larmet kommer att vara tyst när alla problem är klara.

I följande tabell beskrivs de olika larmförhållandena.

### <a name="alarm-conditions"></a>Larmförhållanden
| Status | Severity | Larm | Ops panel LED |
| --- | --- | --- | --- |
| PCM-varning – förlust av DC-ström från en enda PCM |Fel – ingen förlust av redundans |S1 |Modul fel |
| PCM-varning – förlust av DC-ström från en enda PCM |Fel – förlust av uppsägning |S1 |Modul fel |
| PCM-fläkt misslyckas |Fel – förlust av uppsägning |S1 |Modul fel |
| SBB-modul upptäckt PCM-fel |Fel |S1 |Modul fel |
| PCM bort |Konfigurationsfel |Inget |Modul fel |
| Konfigurationsfel för inneslutning |Fel – kritisk |S1 |Modul fel |
| Varning för låg varning |Varning |S1 |Modul fel |
| Hög varningstemperaturvarning |Varning |S1 |Modul fel |
| Över temperaturlarm |Fel – kritisk |S1 |Modul fel |
| I2C buss fel |Fel – förlust av uppsägning |S1 |Modul fel |
| Kommunikationsfel på Ops-panelen (I2C) |Fel – kritisk |S1 |Modul fel |
| Fel på kontrollanten |Fel – kritisk |S1 |Modul fel |
| Fel på SBB-gränssnittsmodul |Fel – kritisk |S1 |Modul fel |
| SBB-gränssnittsmodulfel – Inga fungerande moduler kvar |Fel – kritisk |S4 |Modul fel |
| SBB-gränssnittsmodulen har tagits bort |Varning |Inget |Modul fel |
| Fel på drivströmkontroll |Varning – ingen förlust av drivan |S1 |Modul fel |
| Fel på drivströmkontroll |Fel – kritisk; förlust av drivande effekt |S1 |Modul fel |
| Enheten har tagits bort |Varning |Inget |Modul fel |
| Otillräcklig ström tillgänglig |Varning |ingen |Modul fel |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimples maskinvarukomponenter och status](storsimple-8000-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png


