---
title: Aktivera din enhet i StorSimple 8000-serien eller inaktivera | Microsoft Docs
description: Beskriver hur du aktiverar en ny StorSimple-enhet, aktivera en enhet som stängdes av eller drabbades av strömavbrott och stänga av en aktiv enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95fd00608be9cfafb4c703c32ec3ed4713855ca5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670974"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Aktivera eller inaktivera din enhet i StorSimple 8000-serien

## <a name="overview"></a>Översikt
Det krävs inte att stänga av en Microsoft Azure StorSimple-enhet som en del av normal drift. Du kan dock behöva aktivera en ny enhet eller en enhet som behövde stängas av. I allmänhet krävs en avstängning i fall där du måste ersätta misslyckade maskinvara, fysiskt flytta en enhet eller ta en enhet ur funktion. Den här självstudien beskriver nödvändiga stegen för att aktivera och stänga av din StorSimple-enhet i olika scenarier.

## <a name="turn-on-a-new-device"></a>Aktivera en ny enhet
Stegen för att aktivera en StorSimple-enhet för första gången varierar beroende på om enheten är en 8100 eller 8600-modellen. 8100 har en enda primär enhet medan 8600 är en dubbel-kabinett-enhet med en primär lagringsenhet och ett EBOD-hölje. Detaljerade anvisningar för båda modellerna beskrivs i följande avsnitt.

* [Ny enhet med primära hölje](#new-device-with-primary-enclosure-only)
* [Ny enhet med EBOD hölje](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Ny enhet med primära hölje
StorSimple 8100-modellen är en enda hölje-enhet. Din enhet innehåller redundant ström och kylning moduler (PCMs). Både PCMs måste vara installerat och anslutet till olika strömkällor att säkerställa hög tillgänglighet.

Utför följande steg för att kabelansluta den kraft.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Slutför enhetskonfiguration och kablar anvisningar, finns i [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md). Se till att du följer anvisningarna exakt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Ny enhet med EBOD hölje
StorSimple 8600-modellen har både ett hölje som primär och en EBOD-kabinett. Detta kräver att enheter för att vara kabelansluten tillsammans till seriellt ansluten SCSI (SAS)-anslutning och ström.

När du konfigurerar den här enheten för första gången utför steg för SAS-kablar först och följer sedan anvisningarna för kablar.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Slutför enhetskonfiguration och kablar anvisningar, finns i [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md). Se till att du följer anvisningarna exakt.

## <a name="turn-on-a-device-after-shutdown"></a>Aktivera en enhet efter avstängning
Stegen för att aktivera en StorSimple-enhet när den har stängts av är olika beroende på om enheten är en 8100 eller 8600-modellen. 8100 har en enda primär enhet medan 8600 är en dubbel-kabinett-enhet med en primär lagringsenhet och ett EBOD-hölje.

* [Enhet med primära hölje](#device-with-primary-enclosure-only)
* [Enhet med EBOD hölje](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Enhet med primära hölje
Följ anvisningarna nedan för att aktivera en StorSimple-enhet med en primär hölje och inga EBOD-kabinett efter en avstängning.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aktivera en enhet med en primär hölje
1. Se till att kraften växlar på båda Power och kylning moduler (PCMs) är i OFF-läge. Om växlarna inte OFF-läge, ska vi gå till OFF-läge och vänta tills lamporna att gå.
2. Aktivera enheten genom att vända strömbrytare på båda PCMs till på plats. Enheten bör aktivera.
3. Kontrollera följande för att kontrollera att enheten är helt på:
   
   1. OK-led: ar på båda PCM-modulerna är gröna.
   2. Status led: ar på båda styrenheterna är fast grönt.
   3. Den blå Indikatorn på en av kontrollenheterna blinkande, vilket betyder att kontrollanten är aktiv.
      
      Om något av dessa villkor inte uppfylls, är din enhet inte felfri. . [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD hölje
Använd följande procedur för att aktivera en StorSimple-enhet med en primär lagringsenhet och ett EBOD-hölje efter en avstängning. Utför varje steg i sekvensen exakt så som beskrivs. Det gick inte att göra det kan resultera i dataförlust.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aktivera en enhet med en primär och en EBOD-kabinett
1. Se till att EBOD-hölje är ansluten till primära höljet. Mer information finns i [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrollera att den kraft och kylning moduler (PCMs) på både EBOD och primära bilagor i OFF-läge. Om växlarna inte OFF-läge, ska vi gå till OFF-läge och vänta tills lamporna att gå.
3. Aktivera EBOD-höljet första genom att vända strömbrytare på båda PCMs till på plats. PCM-led: ar vara grön. En grön EBOD-kontrollanten Indikator på den här enheten anger att EBOD-hölje är på.
4. Aktivera primära höljet genom att vända strömbrytare på båda PCMs till på plats. Nu bör hela systemet på.
5. Kontrollera att SAS-led: ar är grön, vilket säkerställer att anslutningen mellan EBOD-höljet och primära höljet är bra.

## <a name="turn-on-a-device-after-a-power-loss"></a>Aktivera en enhet efter strömavbrott
Ett strömavbrott eller avbrott kan stänga av en StorSimple-enhet. Strömavbrott kan inträffa på en av strömförsörjningar eller båda strömförsörjning. Åtgärder för återställning är olika beroende på om enheten är en modell 8100 eller 8600. 8100 har en enda primär enhet medan 8600 är en dubbel-kabinett-enhet med en primär lagringsenhet och ett EBOD-hölje. Det här avsnittet beskriver stegen för återställning för varje scenario.

* [Enhet med primära hölje](#8100)
* [Enhet med EBOD hölje](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Enhet med primära hölje <a name="8100">
Systemet kan fortsätta dess normal drift om strömmen bryts till någon av dess strömförsörjning. Men om du vill garantera hög tillgänglighet för enheten, återställa power till strömförsörjningen så snart som möjligt.

Om det finns ett strömavbrott eller strömavbrott på båda strömkällor, stängs systemet i ett välordnat och kontrollerat sätt. När kraften återupprättas så systemet automatiskt aktiveras.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Enhet med EBOD hölje <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Ange strömavbrott på en power
Systemet kan fortsätta dess normal drift om strömmen bryts till någon av dess strömkällor på primära höljet eller EBOD-höljet. Men om du vill garantera hög tillgänglighet för enheten Återställ power till strömförsörjningen så snart som möjligt.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Strömavbrott på båda strömkällor på primära och EBOD-höljen
Om det finns ett strömavbrott eller strömavbrott på båda strömkällor, EBOD-höljet stängs omedelbart och primära höljet ska stängas av om en ordnad och kontrollerat sätt. När strömmen kommer tillbaka startar installationen automatiskt.

Om kraften är avstängt manuellt ta följande steg för att återställa power i systemet.

1. Aktivera EBOD-höljet.
2. När EBOD-hölje är på kan du aktivera primära höljet.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Strömavbrott på båda strömkällor på EBOD hölje
När du ställer in din kablar, måste du kontrollera att EBOD aldrig ansluten fristående till en separat PDU. Om EBOD och primära kabinett upphör att fungera på samma gång, återställer systemet.

Om det bara EBOD-höljet misslyckas på båda strömkällor, återställer systemet inte automatiskt. Vidta följande steg för att aktivera systemet och återställer den till felfritt tillstånd:

1. Om det primära höljet är aktiverat inaktivera både ström och kylning moduler (PCMs).
2. Vänta några minuter att stänga av systemet.
3. Aktivera EBOD-höljet.
4. När EBOD-hölje är på kan du aktivera primära höljet.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Aktivera en enhet efter primärt och EBOD hölje anslutningen bryts
Om anslutningen bryts mellan kontrollenheten i vänteläge och motsvarande EBOD-kontrollanten, fortsätter enheten att fungera. Om anslutningen mellan den aktiva kontrollenheten system och motsvarande EBOD-kontrollanten tappas bort, växling vid fel ska inträffa och enheten bör fortsätta att fungera som vanligt.

När båda seriellt ansluten SCSI (SAS)-kablar tas bort eller anslutningen mellan EBOD-höljet och primära höljet avskiljes, kommer enheten slutar fungera. Nu ska du utföra följande steg.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Om du vill aktivera enheten efter anslutningen går förlorad
1. Komma åt baksidan av enheten.
2. Om SAS-kabelanslutning mellan EBOD-höljet och primära höljet bryts är alla SAS lane led: ar på EBOD-enheten inaktiverad.
3. Stänga av både ström och kylning moduler (PCMs) på EBOD-höljet och primära höljet.
4. Vänta tills alla lamporna på baksidan av båda höljena inaktivera.
5. Återinföra SAS-kablar och kontrollera att det finns en bra anslutning mellan EBOD-höljet och primära höljet.
6. Aktivera EBOD-höljet först växlar visas båda PCM som till den på positionen.
7. Kontrollera att EBOD-hölje är på genom att kontrollera att grön Indikator har värdet ON.
8. Aktivera primära höljet.
9. Kontrollera att det primära höljet är på genom att kontrollera att domänkontrollanten grön Indikator har värdet ON.
10. Kontrollera att EBOD hölje anslutningen med primära höljet fungerar genom att kontrollera att SAS-lane led: ar (fyra per EBOD-kontrollanten) är alla vidare.

> [!IMPORTANT]
> Om SAS-kablar är defekta eller anslutningen mellan EBOD-höljet och den primära kabinetten är inte bra när du aktiverar systemet, hamnar den i återställningsläge. . [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om detta inträffar.


## <a name="turn-off-a-running-device"></a>Stänga av en aktiv enhet
En som kör StorSimple-enhet kanske måste stängas av om den flyttas, tas bort från tjänsten, eller har en felaktig komponent som måste ersättas. Stegen är olika beroende på om StorSimple-enheten är en 8100 eller 8600-modellen. 8100 har en enda primär enhet medan 8600 är en dubbel-kabinett-enhet med en primär lagringsenhet och ett EBOD-hölje. Det här avsnittet beskriver stegen för att stänga av en aktiv enhet.

* [Enhet med primära hölje](#8100a)
* [Enhet med EBOD hölje](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Enhet med primära hölje <a name="8100a">
För att Stäng enheten på ett välordnat och kontrollerat sätt kan du göra det via Azure portal eller via Windows PowerShell för StorSimple. 

> [!IMPORTANT]
> Stäng inte av en aktiv enhet med hjälp av på strömknappen på baksidan av enheten.
> 
> Innan du stänger av enheten, se till att alla enhetens komponenter är felfria. I Azure-portalen går du till **enheter** > **övervakaren** > **hälsotillstånd för maskinvara**, och kontrollera att status för alla komponenter är grönt. Detta gäller endast för ett felfritt system. Om systemet stängs ned till ersätter en komponent som krånglar, du ser ett misslyckade (rött) eller vara försämrad (gul) status för respektive komponent i den **maskinvarustatus**.
> 
> 

När du har åtkomst till Windows PowerShell för StorSimple- eller Azure-portalen följer du stegen i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Enhet med EBOD hölje <a name="8600a">
> [!IMPORTANT]
> Se till att alla enhetens komponenter är felfria innan du stänger primära höljet och EBOD-kabinett. I Azure-portalen går du till **enheter** > **övervakaren** > **hälsotillstånd för maskinvara**, och kontrollera att alla komponenter är felfria.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Att stänga av en aktiv enhet med EBOD hölje
1. Följ alla steg i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) för primära höljet.
2. När primära höljet datorn stängs av, kan du stänga av EBOD genom att vända av både ström och kylning modulen (PCM) växlar.
3. Kontrollera att alla lamporna på baksidan av EBOD-hölje är inaktiverade för att kontrollera att EBOD har avslutats.

> [!NOTE]
> SAS-kablar som används för att ansluta EBOD-höljet till primära höljet ska inte tas bort förrän efter att systemet stängs av.

## <a name="next-steps"></a>Nästa steg
[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om det uppstår problem när aktivera eller stänga av en StorSimple-enhet.

