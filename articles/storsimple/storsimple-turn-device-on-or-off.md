---
title: Aktivera och inaktivera enheten StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du aktiverar en ny StorSimple-enhet, aktivera en enhet som har stängts av eller drabbades av strömavbrott och stänga av en enhet som körs.
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
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
ms.locfileid: "27779456"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Aktivera eller inaktivera enheten StorSimple 8000-serien

## <a name="overview"></a>Översikt
Det krävs inte att stänga av en Microsoft Azure StorSimple-enhet som en del av normal drift. Du kan dock behöva aktivera en ny enhet eller en enhet som hade stängas av. I allmänhet krävs en avstängning i fall där du måste ersätta misslyckade maskinvara, fysiskt flytta en enhet eller ta en enhet ur funktion. Den här självstudiekursen beskriver nödvändiga proceduren för att aktivera och stänga av din StorSimple-enhet i olika scenarier.

## <a name="turn-on-a-new-device"></a>Aktivera en ny enhet
Stegen för att aktivera en StorSimple-enhet för första gången varierar beroende på om enheten är en 8100 eller en 8600-modell. 8100 har en enda primär enhet medan 8600 är en dubbla hölje-enhet med en primär enhet och en EBOD bilaga. Detaljerade anvisningar för båda modellerna beskrivs i följande avsnitt.

* [Ny enhet med primär enhet](#new-device-with-primary-enclosure-only)
* [Ny enhet med EBOD hölje](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Ny enhet med primär enhet
StorSimple 8100-modellen är en enda enhet. Enheten innehåller redundant ström och kylning moduler (PCMs). Både PCMs måste installeras och ansluten till olika strömkällor att säkerställa hög tillgänglighet.

Utför följande steg för att kabelanslut din enhet för ström.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Slutför enhetskonfiguration och kablar instruktioner finns i [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md). Kontrollera att du följer anvisningarna exakt.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Ny enhet med EBOD hölje
StorSimple 8600-modellen har både en primär enhet och en EBOD bilaga. Detta kräver att enheter ska vara kabelanslutna tillsammans för seriellt ansluten SCSI (SAS)-anslutning och ström.

När du konfigurerar enheten för första gången, utför steg för SAS-kablar först och följer sedan anvisningarna för kablar.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Slutför enhetskonfiguration och kablar instruktioner finns i [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md). Kontrollera att du följer anvisningarna exakt.

## <a name="turn-on-a-device-after-shutdown"></a>Aktivera en enhet efter avstängning
Stegen för att aktivera en StorSimple-enhet när den har avslutats är olika beroende på om enheten är en 8100 eller en 8600-modell. 8100 har en enda primär enhet medan 8600 är en dubbla hölje-enhet med en primär enhet och en EBOD bilaga.

* [Enhet med primär enhet](#device-with-primary-enclosure-only)
* [Enhet med EBOD hölje](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Enhet med primär enhet
Använd följande procedur för att aktivera en StorSimple-enhet med en primär enhet och inga EBOD hölje efter en avstängning.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Aktivera en enhet med en primär enhet
1. Se till att kraften växlar på båda ström och kylning moduler (PCMs) är i INAKTIVERAT läge. Om växlarna inte är i INAKTIVERAT läge Vänd dem till OFF-läge och vänta tills indikeringar att gå.
2. Aktivera enheten genom att vända strömbrytare på båda PCMs till på plats. Enheten ska aktivera.
3. Kontrollera följande för att kontrollera att enheten är helt för:
   
   1. OK indikatorer på båda PCM-modulerna är gröna.
   2. Status indikatorer på båda domänkontrollanterna är fast grönt.
   3. Blå Indikator på en av domänkontrollanterna blinkar, vilket anger att domänkontrollanten är aktiv.
      
      Enheten är inte hälsosam om något av dessa villkor inte uppfylls. Kontrollera [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Enhet med EBOD hölje
Använd följande procedur för att aktivera en StorSimple-enhet med en primär enhet och en EBOD bilaga efter en avstängning. Utför exakt så som det beskrivs varje steg i sekvensen. Det gick inte att göra det kan resultera i förlust av data.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Aktivera en enhet med en primär och en EBOD bilaga
1. Se till att EBOD höljet är ansluten till den primära enhet. Mer information finns i [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).
2. Kontrollera att ström och kylning moduler (PCMs) på både EBOD och primära bilagor i OFF-läge. Om växlarna inte är i INAKTIVERAT läge Vänd dem till OFF-läge och vänta tills indikeringar att gå.
3. Aktivera EBOD höljet första genom att vända strömbrytare på båda PCMs till på plats. PCM-indikatorer ska vara grön. En grön EBOD-styrenhet Indikator på denna enhet anger att EBOD höljet är på.
4. Aktivera primära höljet genom att vända strömbrytare på båda PCMs till på plats. Nu bör hela systemet på.
5. Kontrollera att SAS-indikatorer är grön, vilket säkerställer att anslutningen mellan EBOD höljet och primära höljet är bra.

## <a name="turn-on-a-device-after-a-power-loss"></a>Aktivera en enhet efter strömavbrott
Ett strömavbrott eller avbrott kan stänga av en StorSimple-enhet. Strömavbrott kan bero på något av strömförsörjningar eller båda strömkällor. Steg för återställning är olika beroende på om enheten är en 8100 eller ett 8600-modell. 8100 har en enda primär enhet medan 8600 är en dubbla hölje-enhet med en primär enhet och en EBOD bilaga. Det här avsnittet beskrivs återställning proceduren för varje scenario.

* [Enhet med primär enhet](#8100)
* [Enhet med EBOD hölje](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Enhet med primär enhet<a name="8100">
Systemet kan fortsätta dess normal drift om det finns ett strömavbrott till någon av dess strömkällor. Men om du vill garantera hög tillgänglighet för enheten, återställa power till strömförsörjningen så snart som möjligt.

Om det finns ett strömavbrott eller strömavbrott på båda strömkällor, stängs systemet på ett korrekt och kontrollerat sätt. När kraften återställs ska systemet aktivera automatiskt.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Enhet med EBOD hölje<a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Ange ett strömavbrott på en ström
Systemet kan fortsätta dess normal drift om det finns ett strömavbrott till någon av dess strömkällor på primära höljet eller EBOD höljet. Men om du vill garantera hög tillgänglighet för enheten, Återställ power till strömförsörjningen så snart som möjligt.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Strömavbrott på båda strömkällor på primära och EBOD bilagor
Om det finns ett strömavbrott eller strömavbrott på båda strömkällor, EBOD höljet omedelbart att avslutas av och primära höljet stängs på ett korrekt och kontrollerat sätt. När strömmen återställs startar installationen automatiskt.

Om strömmen är avstängd manuellt ta du följande steg för att återställa power till systemet.

1. Aktivera EBOD höljet.
2. Aktivera primära höljet efter EBOD höljet på.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Strömavbrott på båda strömkällor på EBOD hölje
När du ställer in kablarna måste du kontrollera att EBOD aldrig ansluten enbart till en separat PDU. Om EBOD och primära höljet inte på samma gång, återställer systemet.

Om endast EBOD höljet misslyckas på båda strömkällor, återställer systemet inte automatiskt. Vidta följande steg för att aktivera systemet och återställer den till felfritt tillstånd:

1. Om det primära höljet är aktiverat inaktivera både ström och kylning moduler (PCMs).
2. Vänta några minuter att stänga av systemet.
3. Aktivera EBOD höljet.
4. Aktivera primära höljet efter EBOD höljet på.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Aktivera en enhet när den primära servern och EBOD hölje anslutningen bryts
Om anslutningen bryts mellan vänteläge domänkontrollant och motsvarande EBOD-styrenhet, fortsätter enheten att fungera. Om anslutningen mellan den aktiva styrenheten system och motsvarande EBOD domänkontrollant tappas bort, växling vid fel ska inträffa och enheten bör fortsätta att fungera som vanligt.

När båda seriellt ansluten SCSI (SAS)-kablar har tagits bort eller anslutningen mellan EBOD höljet och primära höljet fjärrdisken slutar enheten den att fungera. Nu ska du utföra följande steg.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Aktivera enheten när anslutningen bryts
1. Åtkomst på baksidan av enheten.
2. Om SAS kabelanslutningen mellan EBOD höljet och primära höljet har brutits, kommer alla SAS lane indikatorer på höljet EBOD vara inaktiverat.
3. Stäng av både ström och kylning moduler (PCMs) på höljet EBOD och primära höljet.
4. Vänta tills alla indikeringar på baksidan av båda höljena inaktivera.
5. Infoga SAS-kablar och kontrollera att det finns en bra anslutning mellan EBOD höljet och primära höljet.
6. Aktivera EBOD höljet första genom att vända båda PCM växlar till på plats.
7. Se till att EBOD höljet på genom att kontrollera att den gröna Indikatorn är ON.
8. Aktivera primära höljet.
9. Se till att det primära höljet på genom att kontrollera att domänkontrollanten grön Indikator är ON.
10. Kontrollera att EBOD hölje anslutningen med primära höljet fungerar genom att kontrollera att SAS lane indikatorer (fyra per EBOD styrenhet) är alla ON.

> [!IMPORTANT]
> Om SAS-kablar är felaktiga eller anslutningen mellan EBOD höljet och primära höljet är inte bra när du aktiverar systemet, försätts den i återställningsläge. Kontrollera [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md) om detta händer.


## <a name="turn-off-a-running-device"></a>Inaktivera en enhet som körs
En aktiv virtuell StorSimple-enhet kan behöva stängas om den flyttas, tas bort från tjänsten, eller har en felaktig komponent som ska ersättas. Stegen är olika beroende på om StorSimple-enheten är en 8100 eller en 8600-modell. 8100 har en enda primär enhet medan 8600 är en dubbla hölje-enhet med en primär enhet och en EBOD bilaga. Det här avsnittet beskrivs stegen för att stänga av en enhet som körs.

* [Enhet med primär enhet](#8100a)
* [Enhet med EBOD hölje](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Enhet med primär enhet<a name="8100a">
Om du vill stänga av enheten på ett korrekt och kontrollerat sätt kan du göra det via Azure-portalen eller via Windows PowerShell för StorSimple. 

> [!IMPORTANT]
> Stäng inte av en enhet som körs med hjälp av strömknappen på baksidan av enheten.
> 
> Kontrollera att alla enhetens komponenter är felfria innan du stänger av enheten. I Azure-portalen går du till **enheter** > **övervakaren** > **maskinvara hälsa**, och kontrollera att status för alla komponenter är grön. Detta gäller endast för ett felfritt system. Om systemet stängs ned till ersätter en felaktig komponent, du ser en misslyckad (röd) eller vara försämrad (gul) status för respektive komponent i den **maskinvarustatus**.
> 
> 

När du har åtkomst till Windows PowerShell för StorSimple eller Azure-portalen, följer du stegen i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Enhet med EBOD hölje<a name="8600a">
> [!IMPORTANT]
> Se till att alla enhetens komponenter är felfria innan du stänger primära höljet och EBOD höljet. I Azure-portalen går du till **enheter** > **övervakaren** > **maskinvara hälsa**, och kontrollera att alla komponenter är felfria.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Att stänga av en enhet som körs med EBOD hölje
1. Följ alla steg i [stänga av en StorSimple-enhet](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) för primära höljet.
2. När primära höljet är avstängd, kan du stänga av EBOD genom att vända av både ström och kylning modul (PCM) växlar.
3. Kontrollera att alla indikeringar på baksidan av EBOD höljet är inaktiverade för att verifiera att EBOD har avslutats.

> [!NOTE]
> SAS-kablar som används för att ansluta EBOD höljet till primära höljet ska inte tas bort förrän efter att datorn är avstängd.

## <a name="next-steps"></a>Nästa steg
[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om du får problem när aktivera eller stänga av en StorSimple-enhet.

