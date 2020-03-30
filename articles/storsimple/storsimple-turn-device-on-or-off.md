---
title: Slå på eller av storsimple 8000-serien
description: I artikeln beskrivs hur du slår på en ny StorSimple-enhet, slår på en enhet som stängdes av eller förlorade ström och stänger av en enhet som körs.
author: alkohli
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1319583569a8abc619ad902a87ee551b476f88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254630"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Slå på eller stänga av storsimple 8000-seriens enhet

## <a name="overview"></a>Översikt
Att stänga av en Microsoft Azure StorSimple-enhet krävs inte som en del av normal systemdrift. Du kan dock behöva slå på en ny enhet eller en enhet som måste stängas av. I allmänhet krävs en avstängning i de fall där du måste ersätta misslyckad maskinvara, fysiskt flytta en enhet eller ta bort en enhet från drift. I den här självstudien beskrivs den procedur som krävs för att aktivera och stänga av StorSimple-enheten i olika scenarier.

## <a name="turn-on-a-new-device"></a>Slå på en ny enhet
Stegen för att slå på en StorSimple-enhet för första gången varierar beroende på om enheten är en 8100- eller en 8600-modell. 8100 har en enda primär kapsling, medan 8600 är en enhet med dubbla kapslingar med en primär kapsling och en EBOD-kapsling. De detaljerade stegen för båda modellerna beskrivs i följande avsnitt.

* [Ny enhet med endast primärhölje](#new-device-with-primary-enclosure-only)
* [Ny enhet med EBOD-hölje](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Ny enhet med endast primärhölje
StorSimple 8100-modellen är en enda kapslingsenhet. Enheten innehåller redundanta ström- och kylmoduler (PCM). Båda pcm måste installeras och anslutas till olika strömkällor för att säkerställa hög tillgänglighet.

Utför följande steg för att kabela enheten för ström.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Instruktioner för fullständig enhetsinstallation och kablar finns i [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md). Se till att du följer instruktionerna exakt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Ny enhet med EBOD-hölje
StorSimple 8600-modellen har både en primär kapsling och en EBOD-hölje. Detta kräver att enheterna kopplas ihop för SAS-anslutning (Serial Attached SCSI) och ström.

När du konfigurerar den här enheten för första gången utför du stegen för SAS-kablar först och slutför sedan stegen för strömkablar.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Instruktioner för fullständig enhetsinstallation och kablar finns i [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md). Se till att du följer instruktionerna exakt.

## <a name="turn-on-a-device-after-shutdown"></a>Aktivera en enhet efter avstängning
Stegen för att slå på en StorSimple-enhet efter att den har stängts av är olika beroende på om enheten är en 8100- eller en 8600-modell. 8100 har en enda primär kapsling, medan 8600 är en enhet med dubbla kapslingar med en primär kapsling och en EBOD-kapsling.

* [Enhet med endast primärhölje](#device-with-primary-enclosure-only)
* [Enhet med EBOD-hölje](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Enhet med endast primärhölje
Efter en avstängning använder du följande procedur för att slå på en StorSimple-enhet med en primär kapsling och ingen EBOD-hölje.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Så här aktiverar du en enhet med endast ett primärt hölje
1. Kontrollera att strömbrytarna på både ström- och kylmoduler (PCM) är i off-läge. Om strömbryn inte är i off-läge, vänd dem sedan till OFF-läge och vänta tills lamporna släcks.
2. Slå på enheten genom att vrida strömbrytarna på båda PCM:erna till påläge. Enheten ska slås på.
3. Kontrollera följande för att kontrollera att enheten är helt på:
   
   1. OK-lysdioderna på båda PCM-modulerna är gröna.
   2. Statuslysdioderna på båda styrenheterna är fast gröna.
   3. Den blå lysdioden på en av handkontrollerna blinkar, vilket indikerar att handkontrollen är aktiv.
      
      Om något av dessa villkor inte uppfylls är enheten inte felfri. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD-hölje
Efter en avstängning använder du följande procedur för att slå på en StorSimple-enhet med en primär kapsling och en EBOD-hölje. Utför varje steg i följd exakt som beskrivs. Om du inte gör det kan data gå förlorade.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Så här slår du på en enhet med en primär och en EBOD-hölje
1. Se till att EBOD-höljet är anslutet till den primära höljet. Mer information finns i [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Se till att energi- och kylmodulerna (PCM) på både EBOD och primära höljen är i OFF-läge. Om strömbryn inte är i off-läge, vänd dem sedan till OFF-läge och vänta tills lamporna släcks.
3. Slå först på EBOD-höljet genom att vrida strömbrytarna på båda PCM:erna till påläge. PCM-lysdioderna ska vara gröna. En grön EBOD-styrstjdiod på denna enhet indikerar att EBOD-höljet är aktiverat.
4. Slå på det primära höljet genom att vrida strömbrytarna på båda PCM:erna till påläge. Hela systemet bör nu vara på.
5. Kontrollera att SAS-lysdioderna är gröna, vilket säkerställer att anslutningen mellan EBOD-höljet och den primära höljet är god.

## <a name="turn-on-a-device-after-a-power-loss"></a>Slå på en enhet efter strömavbrott
Ett strömavbrott eller avbrott kan stänga av en StorSimple-enhet. Strömavbrottet kan inträffa på en av nätaggregaten eller båda nätaggregaten. Återställningsstegen är olika beroende på om enheten är en 8100- eller en 8600-modell. 8100 har en enda primär kapsling, medan 8600 är en enhet med dubbla kapslingar med en primär kapsling och en EBOD-kapsling. I det här avsnittet beskrivs återställningsproceduren för varje scenario.

* [Enhet med endast primärhölje](#8100)
* [Enhet med EBOD-hölje](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Enhet med endast primärhölje<a name="8100">
Systemet kan fortsätta sin normala drift om det finns strömavbrott i en av dess nätaggregat. För att säkerställa hög tillgänglighet för enheten, dock återställa strömmen till strömförsörjningen så snart som möjligt.

Om det sker ett strömavbrott eller strömavbrott på båda nätaggregaten stängs systemet av på ett ordnat och kontrollerat sätt. När strömmen återställs slås systemet automatiskt på.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Enhet med EBOD-hölje<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Strömavbrott på en strömförsörjning
Systemet kan fortsätta sin normala drift om det finns strömavbrott i en av dess nätaggregat på primärhöljet eller EBOD-höljet. För att säkerställa hög tillgänglighet på enheten, vänligen återställa strömmen till strömförsörjningen så snart som möjligt.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Strömförlust på både nätaggregat på primära och EBOD-kapslingar
Om det sker ett strömavbrott eller strömavbrott på båda nätaggregaten stängs EBOD-höljet omedelbart och det primära höljet stängs av på ett ordnat och kontrollerat sätt. När strömmen är tillbaka startar apparaten automatiskt.

Om strömmen stängs av manuellt, vidta följande åtgärder för att återställa strömmen till systemet.

1. Slå på EBOD-höljet.
2. När EBOD-höljet är aktiverat slår du på den primära inneslutningen.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Strömavbrott på båda nätaggregaten i EBOD-höljet
När du ställer in kablarna måste du se till att EBOD aldrig är anslutet ensamt till en separat PDU. Om EBOD och primärhölje misslyckas samtidigt återställs systemet.

Om bara EBOD-höljet inte fungerar på båda strömförsörjningarna återställs inte systemet automatiskt. Gör följande för att aktivera systemet och återställa det till ett felfritt tillstånd:

1. Om det primära höljet är aktiverat stänger du av både energi- och kylmoduler (PCM).
2. Vänta några minuter tills systemet stängs av.
3. Slå på EBOD-höljet.
4. När EBOD-höljet är aktiverat slår du på den primära inneslutningen.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Slå på en enhet när den primära och EBOD-kapslingsanslutningen har gått förlorad
Om anslutningen bryts mellan standby-styrenheten och motsvarande EBOD-styrenhet fortsätter enheten att fungera. Om anslutningen mellan den aktiva styrenheten och motsvarande EBOD-styrenhet går förlorad, bör redundans ske och enheten ska fortsätta att fungera som vanligt.

När båda SAS-kablarna (Serial Attached SCSI) tas bort eller anslutningen mellan EBOD-höljet och den primära höljet är avskuren, slutar enheten att fungera. Utför nu följande steg.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Så här slår du på enheten när anslutningen har gått förlorad
1. Öppna enhetens baksida.
2. Om SAS-kabelanslutningen mellan EBOD-höljet och den primära inneslutningen är bruten, kommer alla SAS körfältslysdioder på EBOD-höljet att vara avstängda.
3. Stäng av både power- och kylmoduler (PCMs) på EBOD-höljet och det primära höljet.
4. Vänta tills alla lampor på baksidan av båda kapslingarna släcks.
5. Sätt tillbaka SAS-kablarna och se till att det finns en bra anslutning mellan EBOD-kapslingen och huvudkapslingen.
6. Slå först på EBOD-höljet genom att vända båda PCM-växlarna till PÅ-läget.
7. Se till att EBOD-höljet är aktiverat genom att kontrollera att den gröna lysdioden är på.
8. Slå på den primära höljet.
9. Se till att den primära höljet är på genom att kontrollera att den gröna lysdioden är på.
10. Kontrollera att EBOD-kapslingsanslutningen med den primära inneslutningen är bra genom att kontrollera att SAS körfältslysdioder (fyra per EBOD-styrenhet) är alla PÅ.

> [!IMPORTANT]
> Om SAS-kablarna är defekta eller om anslutningen mellan EBOD-höljet och det primära höljet inte är bra, kommer den att gå in i återställningsläge när du slår på systemet. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om detta inträffar.


## <a name="turn-off-a-running-device"></a>Stänga av en enhet som körs
En StorSimple-enhet som körs kan behöva stängas av om den flyttas, tas ur drift eller har en komponent som inte fungerar som behöver bytas ut. Stegen är olika beroende på om StorSimple-enheten är en 8100- eller en 8600-modell. 8100 har en enda primär kapsling, medan 8600 är en enhet med dubbla kapslingar med en primär kapsling och en EBOD-kapsling. I det här avsnittet beskrivs stegen för att stänga av en enhet som körs.

* [Enhet med primär hölje](#8100a)
* [Enhet med EBOD-hölje](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Enhet med primär hölje<a name="8100a">
Om du vill stänga av enheten på ett ordnat och kontrollerat sätt kan du göra det via Azure-portalen eller via Windows PowerShell för StorSimple. 

> [!IMPORTANT]
> Stäng inte av en enhet som körs med hjälp av strömbrytaren på enhetens baksida.
> 
> Innan du stänger av enheten ska du se till att alla enhetskomponenter är felfria. I Azure-portalen navigerar du till hälsa**för enhetsövervakarmaskinvara** > **Hardware health** **Devices** > och kontrollerar att statusen för alla komponenter är grön. Detta gäller endast för ett hälsosamt system. Om systemet stängs av för att ersätta en komponent som inte fungerar visas en misslyckad (röd) eller försämrad (gul) status för respektive komponent i **maskinvarustatusen**.
> 
> 

När du har åtkomst till Windows PowerShell för StorSimple eller Azure-portalen följer du stegen i [att stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Enhet med EBOD-hölje<a name="8600a">
> [!IMPORTANT]
> Innan du stänger av den primära höljet och EBOD-höljet ska du se till att alla enhetskomponenter är felfria. I Azure-portalen navigerar du till hälsa**för enhetsövervakarmaskinvara** > **Hardware health** **Devices** > och kontrollerar att alla komponenter är felfria.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Så här stänger du av en löpenhet med EBOD-hölje
1. Följ alla steg som anges i [stäng av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) för den primära höljet.
2. När det primära höljet har stängts av stänger du av EBOD genom att stänga av pcm-switcharna (Power and Cooling Module).
3. Kontrollera att EBOD har stängts av och kontrollera att alla lampor på baksidan av EBOD-inneslutningen är avstängda.

> [!NOTE]
> DE SAS-kablar som används för att ansluta EBOD-höljet till det primära höljet bör inte tas bort förrän systemet har stängts av.

## <a name="next-steps"></a>Nästa steg
[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du stöter på problem när du slår på eller stänger av en StorSimple-enhet.

