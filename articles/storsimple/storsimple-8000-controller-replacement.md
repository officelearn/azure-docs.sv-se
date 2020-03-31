---
title: Byt ut en enhetsstyrenhet i StorSimple 8000-serien | Microsoft-dokument
description: I artikeln beskrivs hur du tar bort och ersätter en eller båda styrenhetsmodulerna på storsimple 8000-serien.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267929"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Byt ut en styrenhetsmodul på StorSimple-enheten
## <a name="overview"></a>Översikt
I den här självstudien beskrivs hur du tar bort och ersätter en eller båda styrenhetsmodulerna i en StorSimple-enhet. Den diskuterar också den underliggande logiken för scenarier för ersättning av en och två kontroller.

> [!NOTE]
> Innan du utför en ersättare för handkontrollen rekommenderar vi att du alltid uppdaterar styrenhetens inbyggda programvara till den senaste versionen.
> 
> För att förhindra skador på Din StorSimple-enhet ska du inte mata ut handkontrollen förrän lysdioderna visas som något av följande:
> 
> * Alla lampor är släckta.
> * LED ![3, Green](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)check ![ikon](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) , och Röda korset ikonen blinkar, och LED 0 och LED 7 är **på**.


I följande tabell visas de scenarier för ersättningsscenarier som stöds.

| Ärende | Ersättningsscenario | Tillämpligt förfarande |
|:--- |:--- |:--- |
| 1 |En styrenhet är i ett misslyckat tillstånd, den andra styrenheten är felfri och aktiv. |[Byte av en enda styrenhet](#replace-a-single-controller), som beskriver [logiken bakom en enda styrenhetsersättning](#single-controller-replacement-logic), samt [ersättningsstegen](#single-controller-replacement-steps). |
| 2 |Båda styrenheterna har misslyckats och kräver ersättning. Chassit, diskarna och diskhöljet är felfria. |[Byte av dubbla styrenheter](#replace-both-controllers), som beskriver [logiken bakom en dubbel styrenhetsersättning](#dual-controller-replacement-logic), samt [ersättningsstegen](#dual-controller-replacement-steps). |
| 3 |Styrenheter från samma enhet eller från olika enheter byts ut. Chassit, diskarna och diskhöljena är felfria. |Ett varningsmeddelande för en plats som inte matchar visas. |
| 4 |En handkontroll saknas och den andra handkontrollen misslyckas. |[Byte av dubbla styrenheter](#replace-both-controllers), som beskriver [logiken bakom en dubbel styrenhetsersättning](#dual-controller-replacement-logic), samt [ersättningsstegen](#dual-controller-replacement-steps). |
| 5 |En eller båda styrenheterna har misslyckats. Du kan inte komma åt enheten via den seriella konsolen eller Windows PowerShell-ommotkoppling. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för en manuell ersättningsprocedur för handkontrollen. |
| 6 |Styrenheterna har en annan byggversion, vilket kan bero på:<ul><li>Styrenheter har en annan programvaruversion.</li><li>Styrenheter har en annan firmware-version.</li></ul> |Om styrenhetens programvaruversioner är olika identifierar ersättningslogiken den och uppdaterar programversionen på ersättningsstyrenheten.<br><br>Om den inbyggda programvaran för styrenheten är olika och den gamla firmwareversionen **inte** kan uppgraderas automatiskt visas ett varningsmeddelande i Azure-portalen. Du bör söka efter uppdateringar och installera uppdateringar av den inbyggda programvaran.</br></br>Om den inbyggda programvaran för styrenheten är olika och den gamla firmware-versionen automatiskt kan uppgraderas, kommer styrenhetens ersättningslogik att upptäcka detta, och när styrenheten startar uppdateras den inbyggda programvaran automatiskt. |

Du måste ta bort en styrenhetsmodul om den har misslyckats. En eller båda styrenheterna kan misslyckas, vilket kan resultera i en enda styrenhet ersättning eller dubbla styrenhet ersättning. För ersättningsprocedurer och logiken bakom dem, se följande:

* [Ersätta en enskild domänkontrollant](#replace-a-single-controller)
* [Byt ut båda styrenheterna](#replace-both-controllers)
* [Ta bort en styrenhet](#remove-a-controller)
* [Infoga en styrenhet](#insert-a-controller)
* [Identifiera den aktiva handkontrollen på enheten](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Innan du tar bort och byter ut en styrenhet bör du läsa säkerhetsinformationen i [StorSimples maskinvarukomponentersättning](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Ersätta en enskild domänkontrollant
När en av de två styrenheterna på Microsoft Azure StorSimple-enheten har misslyckats, inte fungerar eller saknas måste du byta ut en enda styrenhet.

### <a name="single-controller-replacement-logic"></a>Ersättningslogik för en enda styrenhet
I en enda handkontroll ersättning, bör du först ta bort den misslyckade styrenheten. (Den återstående styrenheten i enheten är den aktiva styrenheten.) När du infogar ersättningsstyrenheten inträffar följande åtgärder:

1. Ersättningsstyrenheten börjar omedelbart kommunicera med StorSimple-enheten.
2. En ögonblicksbild av den virtuella hårddisken (VHD) för den aktiva styrenheten kopieras på ersättningsstyrenheten.
3. Ögonblicksbilden ändras så att när ersättningsstyrenheten startar från den här virtuella hårddisken känns den igen som en standby-styrenhet.
4. När ändringarna är klara startar ersättningsstyrenheten som standby-styrenhet.
5. När båda styrenheterna körs är klustret online.

### <a name="single-controller-replacement-steps"></a>Ersättningssteg för en handkontroll
Gör följande om någon av styrenheterna i Microsoft Azure StorSimple-enheten misslyckas. (Den andra styrenheten måste vara aktiv och igång. Om båda styrenheterna misslyckas eller inte fungerar går du till [ersättningssteg med dubbla styrenheter](#dual-controller-replacement-steps).)

> [!NOTE]
> Det kan ta 30 – 45 minuter för styrenheten att starta om och helt återhämta sig från den enda styrenhetens ersättningsprocedur. Den totala tiden för hela proceduren, inklusive att ansluta kablarna, är cirka 2 timmar.


#### <a name="to-remove-a-single-failed-controller-module"></a>Så här tar du bort en enda misslyckad styrenhetsmodul
1. Gå till Tjänsten StorSimple Device Manager i Azure-portalen, klicka på **Enheter**och klicka sedan på namnet på den enhet som du vill övervaka.
2. Gå till **Övervaka > maskinvaruhälsa**. Statusen för antingen Controller 0 eller Controller 1 ska vara röd, vilket indikerar ett fel.
   
   > [!NOTE]
   > Den misslyckade styrenheten i en enda styrenhet ersättning är alltid en standby-styrenhet.
   
3. Använd bild 1 och följande tabell för att hitta den misslyckade styrenhetsmodulen.
   
    ![Bakplan av enhetens primära kapslingsmoduler](./media/storsimple-controller-replacement/IC740994.png)
   
    **Bild 1** Baksidan av StorSimple-enheten
   
   | Label (Etikett) | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrollant 0 |
   | 4 |Kontrollant 1 |
4. På den misslyckade styrenheten tar du bort alla anslutna nätverkskablar från dataportarna. Om du använder en 8600-modell tar du även bort SAS-kablarna som ansluter styrenheten till EBOD-styrenheten.
5. Följ stegen i [ta bort en styrenhet](#remove-a-controller) för att ta bort den felaktiga styrenheten.
6. Installera fabriksbytet på samma plats där den misslyckade styrenheten togs bort. Detta utlöser ersättningslogiken för en enda styrenhet. Mer information finns i [ersättningslogik för en styrenhet](#single-controller-replacement-logic).
7. Medan ersättningslogiken för en enda styrenhet fortskrider i bakgrunden ansluter du kablarna igen. Var noga med att ansluta alla kablar exakt på samma sätt som de var anslutna innan bytet.
8. När styrenheten har startats om kontrollerar du **controller-status** och **klusterstatusen** i Azure-portalen för att kontrollera att styrenheten är tillbaka i felfritt tillstånd och är i vänteläge.

> [!NOTE]
> Om du övervakar enheten via seriekonsolen kan du se flera omstarter medan handkontrollen återställs från ersättningsproceduren. När den seriella konsolmenyn visas vet du att ersättningen är klar. Om menyn inte visas inom två timmar efter att du har startat bytet av handkontrollen kontaktar du [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Starta uppdatering 4 kan du också `Get-HCSControllerReplacementStatus` använda cmdleten i Windows PowerShell-gränssnittet på enheten för att övervaka status för styrenhetens ersättningsprocess.
> 

## <a name="replace-both-controllers"></a>Byt ut båda styrenheterna
När båda styrenheterna på Microsoft Azure StorSimple-enheten har misslyckats, inte fungerar eller saknas måste du ersätta båda styrenheterna. 

### <a name="dual-controller-replacement-logic"></a>Ersättningslogik med dubbla styrenheter
I en ersättning med dubbla styrenheter tar du först bort båda de felaktiga styrenheterna och infogar sedan ersättningar. När de två ersättningskontrollanterna infogas in in sker följande åtgärder:

1. Utbytesregulatorn i fack 0 kontrollerar följande:
   
   1. Är det med nuvarande versioner av firmware och programvara?
   2. Är det en del av klustret?
   3. Körs peer-styrenheten och är den klustrade?
      
      Om inget av dessa villkor är sant söker handkontrollen efter den senaste dagliga säkerhetskopieringen (finns i **ickeDOMstorage** på enhet S). Styrenheten kopierar den senaste ögonblicksbilden av den virtuella hårddisken från säkerhetskopian.
2. Styrenheten i fack 0 använder ögonblicksbilden för att avbilda sig själv.
3. Under tiden väntar handkontrollen i fack 1 på att handkontrollen 0 ska slutföra avbildningen och starta.
4. När styrenheten 0 startar identifierar styrenhet 1 klustret som skapats av styrenheten 0, vilket utlöser ersättningslogiken för en enda styrenhet. Mer information finns i [ersättningslogik för en styrenhet](#single-controller-replacement-logic).
5. Efteråt kommer båda styrenheterna att köras och klustret kommer att anslutas.

> [!IMPORTANT]
> Efter en dubbel styrenhet ersättning, efter StorSimple enheten är konfigurerad, är det viktigt att du tar en manuell säkerhetskopia av enheten. Dagliga säkerhetskopieringar av enhetskonfiguration utlöses inte förrän efter 24 timmar har förflutit. Arbeta med [Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att göra en manuell säkerhetskopiering av enheten.


### <a name="dual-controller-replacement-steps"></a>Ersättningssteg med dubbla styrenheter
Det här arbetsflödet krävs när båda styrenheterna i Microsoft Azure StorSimple-enheten har misslyckats. Detta kan inträffa i ett datacenter där kylsystemet slutar fungera, och som ett resultat misslyckas båda styrenheterna inom en kort tidsperiod. Beroende på om StorSimple-enheten är avstängd eller påslagen, och om du använder en 8600- eller 8100-modell, krävs en annan uppsättning steg.

> [!IMPORTANT]
> Det kan ta 45 minuter till 1 timme för styrenheten att starta om och helt återhämta sig från en dubbel styrenhet ersättningsprocedur. Den totala tiden för hela proceduren, inklusive att ansluta kablarna, är cirka 2,5 timmar.

#### <a name="to-replace-both-controller-modules"></a>Så här byter du ut båda styrenhetsmodulerna
1. Om enheten är avstängd hoppar du över det här steget och går vidare till nästa steg. Om enheten är påslagen stänger du av enheten.
   
   1. Om du använder en 8600-modell stänger du av den primära höljet först och stänger sedan av EBOD-höljet.
   2. Vänta tills enheten har stängts av helt. Alla lysdioder på baksidan av enheten kommer att vara avstängda.
2. Ta bort alla nätverkskablar som är anslutna till dataportarna. Om du använder en 8600-modell tar du även bort SAS-kablarna som ansluter det primära höljet till EBOD-höljet.
3. Ta bort båda styrenheterna från StorSimple-enheten. Mer information finns i [ta bort en styrenhet](#remove-a-controller).
4. Sätt i fabriksbytet för Controller 0 först och sätt sedan in Controller 1. Mer information finns i [infoga en styrenhet](#insert-a-controller). Detta utlöser ersättningslogiken för dubbla styrenheter. Mer information finns i [ersättningslogik med dubbla styrenheter](#dual-controller-replacement-logic).
5. Medan styrenhetens ersättningslogik fortskrider i bakgrunden ansluter du kablarna igen. Var noga med att ansluta alla kablar exakt på samma sätt som de var anslutna innan bytet. Se detaljerade instruktioner för din modell i avsnittet Kabel din enhet installera [din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
6. Slå på StorSimple-enheten. Om du använder en 8600-modell:
   
   1. Kontrollera att EBOD-höljet är aktiverat först.
   2. Vänta tills EBOD-höljet är igång.
   3. Slå på den primära höljet.
   4. När den första styrenheten startas om och är i felfritt tillstånd körs systemet.
      
      > [!NOTE]
      > Om du övervakar enheten via seriekonsolen kan du se flera omstarter medan handkontrollen återställs från ersättningsproceduren. När den seriella konsolmenyn visas vet du att ersättningen är klar. Om menyn inte visas inom 2,5 timmar efter att du har startat bytet av handkontrollen kontaktar du [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Ta bort en styrenhet
Använd följande procedur för att ta bort en felaktig styrenhetsmodul från StorSimple-enheten.

> [!NOTE]
> Följande illustrationer är avsedda för styrenhet 0. För controller 1 skulle dessa vändas.


#### <a name="to-remove-a-controller-module"></a>Så här tar du bort en styrenhetsmodul
1. Ta tag i modulspärren mellan tummen och pekfingret.
2. Tryck försiktigt ihop tummen och pekfingret för att lossa handkontrollens spärr.
   
    ![Släppa kontrollantspärr](./media/storsimple-controller-replacement/IC741047.png)
   
    **Bild 2** Släppa kontrollantspärr
3. Använd spärren som handtag för att skjuta ut handkontrollen ur chassit.
   
    ![Glidande handkontroll av chassit](./media/storsimple-controller-replacement/IC741048.png)
   
    **Bild 3** Att skjuta ut handkontrollen ur chassit

## <a name="insert-a-controller"></a>Infoga en styrenhet
Använd följande procedur för att installera en modul medföljer styrenhet när du har tagit bort en felaktig modul från StorSimple-enheten.

#### <a name="to-install-a-controller-module"></a>Så här installerar du en styrenhetsmodul
1. Kontrollera om det finns några skador på gränssnittskontakterna. Installera inte modulen om någon av kontaktstiften är skadad eller böjd.
2. Skjut in styrenhetsmodulen i chassit medan spärren är helt frigjord.
   
    ![Skjut in handkontrollen i chassit](./media/storsimple-controller-replacement/IC741053.png)
   
    **Bild 4** Skjut in handkontrollen i chassit
3. När styrenhetens modul är isatt börjar du stänga spärren samtidigt som du fortsätter att trycka in styrenhetsmodulen i chassit. Spärren kommer att aktiveras för att styra handkontrollen på plats.
   
    ![Stängningskontrollantspärr](./media/storsimple-controller-replacement/IC741054.png)
   
    **Bild 5** Stänga kontrollkontrollens spärr
4. Du är klar när spärren snäpper på plats. OK-lysdioden ska nu vara på. **OK**
   
   > [!NOTE]
   > Det kan ta upp till 5 minuter innan handkontrollen och lysdioden aktiveras.
  
5. För att kontrollera att ersättningen lyckas går du till enheten i Azure-portalen och navigerar sedan till **Övervaka** > **maskinvarans hälsa**och se till att både styrenheten 0 och styrenheten 1 är felfria (statusen är grön).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiera den aktiva handkontrollen på enheten
Det finns många situationer, till exempel registrering av enheter för första gången eller byte av styrenhet, som kräver att du hittar den aktiva handkontrollen på en StorSimple-enhet. Den aktiva styrenheten bearbetar alla diskens inbyggda programvara och nätverksåtgärder. Du kan använda någon av följande metoder för att identifiera den aktiva styrenheten:

* [Använd Azure-portalen för att identifiera den aktiva styrenheten](#use-the-azure-portal-to-identify-the-active-controller)
* [Använda Windows PowerShell för StorSimple för att identifiera den aktiva styrenheten](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Kontrollera den fysiska enheten för att identifiera den aktiva styrenheten](#check-the-physical-device-to-identify-the-active-controller)

Var och en av dessa förfaranden beskrivs därefter.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Använd Azure-portalen för att identifiera den aktiva styrenheten
I Azure-portalen navigerar du till enheten och **övervakar** > sedan**maskinvaruhälsan**och bläddrar till avsnittet **Controllers.** Här kan du kontrollera vilken styrenhet som är aktiv.

![Identifiera aktiv styrenhet i Azure-portalen](./media/storsimple-controller-replacement/IC752072.png)

**Bild 6** Azure-portal som visar den aktiva styrenheten

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Använda Windows PowerShell för StorSimple för att identifiera den aktiva styrenheten
När du öppnar enheten via seriekonsolen visas ett bannermeddelande. Banderollmeddelandet innehåller grundläggande enhetsinformation som modell, namn, installerad programvaruversion och status för den styrenhet som du använder. Följande bild visar ett exempel på ett banderollmeddelande:

![Meddelande om seriell banderoll](./media/storsimple-controller-replacement/IC741098.png)

**Bild 7** Banderollmeddelande som visar styrenhet 0 som aktiv

Du kan använda banderollmeddelandet för att avgöra om handkontrollen du är ansluten till är aktiv eller passiv.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Kontrollera den fysiska enheten för att identifiera den aktiva styrenheten
Om du vill identifiera den aktiva styrenheten på enheten letar du reda på den blå lysdioden ovanför DATA 5-porten på baksidan av den primära höljet.

Om denna lysdiod blinkar är handkontrollen aktiv och den andra handkontrollen i vänteläge. Använd följande diagram och tabell som hjälpmedel.

![Enhetens primära hölje backplan med dataportar](./media/storsimple-controller-replacement/IC741055.png)

**Bild 8** Baksidan av primärhölje med dataportar och övervakningslysdioder

| Label (Etikett) | Beskrivning |
|:--- |:--- |
| 1-6 |DATA 0 – 5 nätverksportar |
| 7 |Blå LED |

## <a name="next-steps"></a>Nästa steg
Läs mer om byte av [Maskinvarukomponent i StorSimple](storsimple-8000-hardware-component-replacement.md).

