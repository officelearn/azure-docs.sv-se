---
title: Ersätt enhetskontrollanten en StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du tar bort och ersätter en eller båda controller-moduler på din enhet i StorSimple 8000-serien.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61482914"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Ersätta en controller-modulen på StorSimple-enheten
## <a name="overview"></a>Översikt
Den här självstudien beskrivs hur du tar bort och ersätter en eller båda controller-moduler i en StorSimple-enhet. Det innehåller också information om den underliggande logiken för enkla och dubbla domänkontrollant ersättning scenarier.

> [!NOTE]
> Innan du utför en domänkontrollant ersättning, rekommenderar vi att du uppdaterar din inbyggd programvara för controller alltid till den senaste versionen.
> 
> Att skada din StorSimple-enhet, du inte matar kontrollanten tills de led: ar visas som något av följande:
> 
> * Alla lamporna är OFF.
> * LED 3, ![grön kryssikon](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), och ![röda krysset ikonen](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) är blinkar och LED 0 och 7 LED är **på**.


I följande tabell visas scenarierna för ersättning av stöds controller.

| Fall | Ersättning scenario | Aktuella proceduren |
|:--- |:--- |:--- |
| 1 |En kontrollenhet är i ett felaktigt tillstånd, den andra styrenheten är felfri och aktiv. |[Enkel controller ersättning](#replace-a-single-controller), som beskriver den [logiken bakom en enda domänkontrollant ersättning](#single-controller-replacement-logic), samt de [ersättning steg](#single-controller-replacement-steps). |
| 2 |Båda styrenheterna har misslyckats och kräva ersättning. De chassin, diskar och hölje är hälsosamma. |[Dubbel domänkontrollant ersättning](#replace-both-controllers), som beskriver den [logiken bakom en dubbel domänkontrollant ersättning](#dual-controller-replacement-logic), samt de [ersättning steg](#dual-controller-replacement-steps). |
| 3 |Domänkontrollanter från samma enhet eller från olika enheter växlas ut. De chassin, diskar och diskenheter är hälsosamma. |Varningsmeddelande för matchning av datatyp som ska användas som platsen visas. |
| 4 |En domänkontrollant saknas och den andra styrenheten misslyckas. |[Dubbel domänkontrollant ersättning](#replace-both-controllers), som beskriver den [logiken bakom en dubbel domänkontrollant ersättning](#dual-controller-replacement-logic), samt de [ersättning steg](#dual-controller-replacement-steps). |
| 5 |En eller båda styrenheterna har misslyckats. Du kan inte komma åt enheten via seriekonsolen eller Windows PowerShell-fjärrkommunikation. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) manuell controller ersättning anvisningar. |
| 6 |Styrenheterna har en annan version, vilket kan bero på följande:<ul><li>Domänkontrollanter har olika version.</li><li>Domänkontrollanter har en annan firmware-version.</li></ul> |Om programvaruversioner controller är olika, upptäcker att logiken som ersättning och uppdaterar programvaruversionen på kontrollanten ersättning.<br><br>Om domänkontrollanten-versioner av inbyggd programvara är olika och den gamla versionen för inbyggd programvara är **inte** automatiskt kan uppgraderas ett varningsmeddelande visas i Azure-portalen. Du ska söka efter uppdateringar och installera uppdateringar av inbyggd programvara.</br></br>Om skiljer sig controller-versioner av inbyggd programvara och den gamla versionen för inbyggd programvara kan automatiskt uppgraderas, controller ersättning logiken identifierar detta och när kontrollanten har startat den inbyggda programvaran uppdateras automatiskt. |

Du måste ta bort en domänkontrollant modul om det har misslyckats. En eller båda controller moduler kan misslyckas, vilket kan resultera i en enskild domänkontrollant ersättare eller dubbel domänkontrollant ersättning. Ersättning procedurer och logiken bakom dem finns i följande:

* [Ersätta en enskild domänkontrollant](#replace-a-single-controller)
* [Ersätt båda styrenheterna](#replace-both-controllers)
* [Ta bort en domänkontrollant](#remove-a-controller)
* [Infoga en kontrollant](#insert-a-controller)
* [Identifiera den aktiva kontrollanten på din enhet](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Innan du tar bort och ersätter en kontrollant att granska säkerhetsinformation i [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Ersätta en enskild domänkontrollant
När en av de två styrenheterna på Microsoft Azure StorSimple-enheten har misslyckats, inte fungerar eller saknar, måste du ersätta en enskild domänkontrollant.

### <a name="single-controller-replacement-logic"></a>Enskild domänkontrollant ersättning logik
I en enskild domänkontrollant ersättning, bör du först ta bort den misslyckade styrenheten. (Den återstående controller i enheten är den aktiva kontrollenheten.) När du infogar kontrollanten ersättning utförs följande åtgärder:

1. Kontrollanten ersättning startar direkt kommunicera med StorSimple-enheten.
2. En ögonblicksbild av den virtuella hårddisken (VHD) för den aktiva kontrollenheten kopieras på kontrollanten ersättning.
3. Ögonblicksbilden har ändrats så att när kontrollanten ersättning startar från den här virtuella Hårddisken, det tolkas som en kontrollenhet i vänteläge.
4. När ändringarna har slutförts startar kontrollanten ersättning som kontrollenheten i vänteläge.
5. När båda styrenheterna kör är klustret online.

### <a name="single-controller-replacement-steps"></a>Enskild domänkontrollant ersättning steg
Utför följande steg om en av kontrollenheterna i din Microsoft Azure StorSimple-enhet misslyckas. (Den andra styrenheten måste vara aktiv och körs. Om båda styrenheterna misslyckas eller fungera, går du till [dual-styrenheten ersättning steg](#dual-controller-replacement-steps).)

> [!NOTE]
> Det kan ta 30 – 45 minuter att starta om och återställa helt från styrenhet ersättning procedur-styrenhet. Den totala tiden för hela proceduren, är inklusive koppla kablar, cirka 2 timmar.


#### <a name="to-remove-a-single-failed-controller-module"></a>Ta bort en enskild misslyckade domänkontrollant-modul
1. Gå till StorSimple Device Manager-tjänsten i Azure-portalen, klickar du på **enheter**, och klicka sedan på namnet på den enhet som du vill övervaka.
2. Gå till **övervakaren > hälsotillstånd för maskinvara**. Status för kontrollenhet 0 eller kontrollenhet 1 ska vara röd, vilket betyder att ett fel.
   
   > [!NOTE]
   > Misslyckade controller i en enskild domänkontrollant ersättning är alltid en kontrollenhet i vänteläge.
   
3. Använd bild 1 och i följande tabell för att hitta modulen misslyckade controller.
   
    ![Serverdelen av enhetens primära Höljesmoduler](./media/storsimple-controller-replacement/IC740994.png)
   
    **Bild 1** tillbaka av StorSimple-enhet
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollenhet 0 |
   | 4 |Kontrollenhet 1 |
4. Ta bort alla anslutna nätverkskablarna från data hamnar på kontrollanten misslyckades. Om du använder en 8600 modell kan du också ta bort SAS-kablar som ansluter kontrollanten till EBOD-kontrollanten.
5. Följ stegen i [ta bort en kontrollant](#remove-a-controller) att ta bort den misslyckade styrenheten.
6. Installera factory ersättningen på samma plats som den misslyckade styrenheten har tagits bort. Detta utlöser styrenhet ersättning logiken. Mer information finns i [enkel controller ersättning logic](#single-controller-replacement-logic).
7. Medan logiken som enskild domänkontrollant ersättning fortsätter i bakgrunden, Återanslut kablarna. Var noga med för att ansluta alla kablar exakt på samma sätt som de var anslutna innan ersättningen.
8. När kontrollanten har startats om kontrollerar du den **status för domänkontrollanten** och **kluster status** i Azure portal för att kontrollera att kontrollanten är tillbaka till felfritt tillstånd och är i vänteläge.

> [!NOTE]
> Om du övervakar enheten via seriekonsolen, eventuellt flera omstarter när kontrollanten håller på att återställas från den ersättning proceduren. När menyn för seriekonsolen visas vet att ersättningen har slutförts. Om inte visas menyn inom två timmar för att starta ersättningen controller [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Uppdatering 4 startar du kan också använda cmdlet: en `Get-HCSControllerReplacementStatus` i Windows PowerShell-gränssnittet på enheten för att övervaka status för controller ersättningsprocessen.
> 

## <a name="replace-both-controllers"></a>Ersätt båda styrenheterna
När båda styrenheterna på Microsoft Azure StorSimple-enheten har misslyckats, är inte fungerar eller saknar, måste du ersätta båda styrenheterna. 

### <a name="dual-controller-replacement-logic"></a>Dubbel domänkontrollant ersättning logik
I en dubbel domänkontrollant ersättning kommer du först ta bort båda misslyckade styrenheterna och sedan infogar ersättningar. När två ersättning styrenheterna infogas utförs följande åtgärder:

1. Kontrollanten ersättning i fack 0 kontrollerar följande:
   
   1. Använder den aktuella versioner av inbyggd programvara och programvara?
   2. Är det en del av klustret?
   3. Är peer-domänkontrollanten körs och är klustrad?
      
      Om ingen av dessa villkor är uppfyllda kontrollanten söker efter den senaste daglig säkerhetskopieringen (som finns i den **nonDOMstorage** på enheten S). Kontrollanten kopierar den senaste ögonblicksbilden av den virtuella Hårddisken från säkerhetskopian.
2. Kontrollanten i fack 0 använder ögonblicksbilden för att avbilda själva.
3. Under tiden kan controller i fack 1 väntar styrenhet 0 och slutföra avbildning som du kan starta.
4. När kontrollenhet 0 startas, identifierar kontrollenhet 1 klustret som skapats av kontrollenhet 0, som utlöser styrenhet ersättning logiken. Mer information finns i [enkel controller ersättning logic](#single-controller-replacement-logic).
5. Därefter körs båda styrenheterna och klustret kommer att försättas online.

> [!IMPORTANT]
> Efter en dubbel domänkontrollant ersättning när StorSimple-enheten har konfigurerats är det viktigt att du utför en manuell säkerhetskopiering av enheten. Dagliga säkerhetskopior för konfiguration av enheter utlöses inte förrän efter 24 timmar har gått. Arbeta med [Microsoft Support](storsimple-8000-contact-microsoft-support.md) att göra en manuell säkerhetskopiering av din enhet.


### <a name="dual-controller-replacement-steps"></a>Dubbel domänkontrollant ersättning steg
Det här arbetsflödet är obligatorisk när båda styrenheterna i din Microsoft Azure StorSimple-enhet har misslyckats. Detta kan inträffa i ett datacenter där kylning systemet slutar fungera och därför båda styrenheterna misslyckas inom en kort tidsperiod. Beroende på om StorSimple-enheten är avstängd eller på och om du använder en 8600 eller en 8100-modell, krävs en annan uppsättning steg.

> [!IMPORTANT]
> Det kan ta 45 minuter till 1 timme att starta om och återställa helt från en dubbel domänkontrollant ersättning procedur-styrenhet. Den totala tiden för hela proceduren, är inklusive koppla kablar, cirka 2,5 timmar.

#### <a name="to-replace-both-controller-modules"></a>Ersätt båda controller-moduler
1. Om enheten är avstängd, hoppa över det här steget och gå vidare till nästa steg. Om enheten är påslagen, stänga av enheten.
   
   1. Om du använder en modell 8600 först tar bort primära höljet och inaktiverar EBOD-höljet.
   2. Vänta tills enheten har avslutats helt. Alla led: ar på baksidan enheten är inaktiverad.
2. Ta bort alla nätverkskablarna som är anslutna till dataportar. Om du använder en 8600 modell kan du också ta bort SAS-kablar som ansluter primära höljet till EBOD-höljet.
3. Ta bort båda styrenheterna från StorSimple-enheten. Mer information finns i [ta bort en kontrollant](#remove-a-controller).
4. Infoga factory ersättning för kontrollenhet 0 först och sedan infogar kontrollenhet 1. Mer information finns i [infoga en kontrollant](#insert-a-controller). Detta utlöser dual-styrenheten ersättning logiken. Mer information finns i [dual-styrenheten ersättning logic](#dual-controller-replacement-logic).
5. När domänkontrollanten ersättning logiken fortsätter i bakgrunden, Återanslut kablarna. Var noga med för att ansluta alla kablar exakt på samma sätt som de var anslutna innan ersättningen. Avsnittet de detaljerade anvisningarna för din modell kabeln din enhet i [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
6. Aktivera StorSimple-enheten. Om du använder en modell 8600:
   
   1. Se till att EBOD-höljet aktiveras först.
   2. Vänta tills EBOD-höljet körs.
   3. Aktivera primära höljet.
   4. När den första enheten startas om och är i felfritt tillstånd kan körs systemet.
      
      > [!NOTE]
      > Om du övervakar enheten via seriekonsolen, eventuellt flera omstarter när kontrollanten håller på att återställas från den ersättning proceduren. När menyn för seriekonsolen visas vet att ersättningen har slutförts. Om inte visas menyn inom 2,5 timmar för att starta ersättningen controller [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Ta bort en domänkontrollant
Använd följande procedur för att ta bort en felaktiga controller-modul från din StorSimple-enhet.

> [!NOTE]
> Följande bilder är för kontrollenhet 0. För kontrollenhet 1 skulle dessa ångras.


#### <a name="to-remove-a-controller-module"></a>Ta bort en domänkontrollant-modul
1. Rapportelementen modulen spärr mellan tummen och pekfingret.
2. Försiktigt klämma dina tummen och pekfingret tillsammans att frigöra controller spärr.
   
    ![Släpper controller spärr](./media/storsimple-controller-replacement/IC741047.png)
   
    **Bild 2** frisläppning spärren för domänkontrollant
3. Använd låset som en referens till bild kontrollanten utanför chassit.
   
    ![Glidande domänkontrollant utanför chassi](./media/storsimple-controller-replacement/IC741048.png)
   
    **Bild 3** glidande kontrollanten utanför chassit

## <a name="insert-a-controller"></a>Infoga en kontrollant
Använd följande procedur för att installera en levererats från fabriken controller-modulen när du tar bort en felaktig modul från din StorSimple-enhet.

#### <a name="to-install-a-controller-module"></a>Installera en domänkontrollant-modul
1. Kontrollera om det finns skador till gränssnittet kopplingar. Installera inte modulen om någon av koppling PIN-koder har skadats eller böjda.
2. Dra modulen controller i chassit medan spärren har släppts helt.
   
    ![Glidande controller i chassi](./media/storsimple-controller-replacement/IC741053.png)
   
    **Bild 4** glidande controller i chassit
3. Börja stänger låset medan du fortsätter att skicka controller-modulen till chassit med modulen controller infogas. Låset som engagerar att vägleda kontrollanten på plats.
   
    ![Avslutande controller spärr](./media/storsimple-controller-replacement/IC741054.png)
   
    **Bild 5** stänger controller spärr
4. Du är klar när låset fästs på plats. Den **OK** LED bör nu finnas på.
   
   > [!NOTE]
   > Det kan ta upp till 5 minuter för kontrollanten och LED att aktivera.
  
5. Om du vill verifiera att ersättningen har genomförts i Azure-portalen, gå till din enhet och gå sedan till **övervakaren** > **hälsotillstånd för maskinvara**, och se till att både styrenhet 0 och controller 1 är felfria (statusen är grön).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiera den aktiva kontrollanten på din enhet
Det finns många situationer, till exempel registrering eller domänkontrollant ersättning för första gången enheten, som uppmanar dig att hitta den aktiva kontrollenheten på en StorSimple-enhet. Den aktiva kontrollenheten bearbetar alla disk av inbyggd programvara och nätverk åtgärder. Du kan använda någon av följande metoder för att identifiera den aktiva kontrollenheten:

* [Använd Azure portal för att identifiera den aktiva kontrollanten](#use-the-azure-portal-to-identify-the-active-controller)
* [Använda Windows PowerShell för StorSimple för att identifiera den aktiva kontrollanten](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Kontrollera den fysiska enheten för att identifiera den aktiva kontrollenheten](#check-the-physical-device-to-identify-the-active-controller)

Var och en av de här procedurerna beskrivs nedan.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Använd Azure portal för att identifiera den aktiva kontrollanten
Navigera till din enhet i Azure-portalen och sedan till **övervakaren** > **hälsotillstånd för maskinvara**, och bläddra till den **domänkontrollanter** avsnittet. Här kan du kontrollera vilken domänkontrollant som är aktiv.

![Identifiera aktiva kontrollenheten i Azure-portalen](./media/storsimple-controller-replacement/IC752072.png)

**Bild 6** Azure-portalen visar den aktiva kontrollenheten

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Använda Windows PowerShell för StorSimple för att identifiera den aktiva kontrollanten
När du har åtkomst till din enhet via seriekonsolen visas Banderollmeddelandet. Banderollmeddelandet innehåller grundläggande information, till exempel modell, namn, version installerad programvara och status för den domänkontrollant som du ansluter till. Följande bild visar ett exempel på Banderollmeddelandet:

![Seriell Banderollmeddelandet](./media/storsimple-controller-replacement/IC741098.png)

**Bild 7** banderoll meddelande som visar kontrollenhet 0 som aktiv

Du kan använda Banderollmeddelandet för att avgöra om den domänkontrollant som du är ansluten till är aktiva eller passiva.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Kontrollera den fysiska enheten för att identifiera den aktiva kontrollenheten
Att identifiera den aktiva kontrollenheten på din enhet, leta upp det blå fältet LEDDE överskrider DATA 5 port på baksidan av primära höljet.

Om den här LED blinkar kontrollanten är aktiv och den andra styrenheten är i vänteläge. Använd följande diagram och tabell som hjälp.

![Enhetens primära hölje bakplan med dataportar](./media/storsimple-controller-replacement/IC741055.png)

**Bild 8** baksidan av primär enhet med dataportar och övervaknings-led: ar

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1-6 |DATA 0 – 5 nätverksportar |
| 7 |Blå Indikator |

## <a name="next-steps"></a>Nästa steg
Läs mer om [StorSimple maskinvaruersättning komponenten](storsimple-8000-hardware-component-replacement.md).

