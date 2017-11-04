---
title: "Ersätta en domänkontrollant för StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du tar bort och ersätter en eller båda controller moduler på StorSimple-enheten."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: e25b52b7-60f5-47f3-bffc-6c157d57ab5d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/03/2017
ms.author: alkohli
ms.openlocfilehash: 5dd5ffc7c08fcc9263b91ca5ac86de5163f91657
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Ersätta en domänkontrollant modul på din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du tar bort och ersätter en eller båda controller moduler i en StorSimple-enhet. Här beskrivs även den underliggande logiken för enkla och dubbla domänkontrollant ersättning scenarier.

> [!NOTE]
> Innan du utför en annan domänkontrollant, rekommenderar vi att du uppdaterar den inbyggda programvaran domänkontrollant alltid till den senaste versionen.
> 
> Att skada din StorSimple-enhet, du inte matar styrenheten tills indikatorer visas som något av följande:
> 
> * Alla ljus är AVSTÄNGD.
> * Indikator 3 ![grön kryssikonen](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), och ![rött kryss ikonen](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) är blinkande och Indikator 0 och Indikator 7 **på**.
> 
> 

I följande tabell visas scenarierna för ersättning av stöds domänkontrollant.

| Ärende | Ersättning scenario | Proceduren |
|:--- |:--- |:--- |
| 1 |En domänkontrollant är i ett felaktigt tillstånd, den andra styrenheten är felfri och aktiva. |[Enkel domänkontrollant ersättning](#replace-a-single-controller), vilket beskriver den [logiken bakom en enda domänkontrollant ersättning](#single-controller-replacement-logic), samt de [ersättning steg](#single-controller-replacement-steps). |
| 2 |Båda domänkontrollanterna har misslyckats och kräva ersättning. Chassi, diskar och diskhölje är hälsosamma. |[Dubbla domänkontrollant ersättning](#replace-both-controllers), vilket beskriver den [logiken bakom en dubbel domänkontrollant ersättning](#dual-controller-replacement-logic), samt de [ersättning steg](#dual-controller-replacement-steps). |
| 3 |Domänkontrollanter från samma enhet eller från olika enheter bytts. Chassi, diskar och diskenheter är hälsosamma. |Varningsmeddelande för matchning av datatyp som ska användas som plats visas. |
| 4 |En domänkontrollant saknas och den andra styrenheten misslyckas. |[Dubbla domänkontrollant ersättning](#replace-both-controllers), vilket beskriver den [logiken bakom en dubbel domänkontrollant ersättning](#dual-controller-replacement-logic), samt de [ersättning steg](#dual-controller-replacement-steps). |
| 5 |En eller båda domänkontrollanter har misslyckats. Du kan inte komma åt enheten via seriekonsolen eller Windows PowerShell-fjärrkommunikation. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) manuell controller ersättning anvisningar. |
| 6 |Domänkontrollanter har en annan version, vilket kan bero på följande:<ul><li>Domänkontrollanter har en annan programvaruversion.</li><li>Domänkontrollanter har en annan firmware-version.</li></ul> |Om domänkontrollanten programvaruversioner är olika, upptäcker att ersättning logik och uppdaterar programvaruversionen på styrenheten för ersättning.<br><br>Om domänkontrollanten firmware-versioner är olika och den gamla versionen av inbyggd programvara är **inte** automatiskt kan uppgraderas ett varningsmeddelande visas i den klassiska Azure-portalen. Du ska söka efter uppdateringar och installera firmware-uppdateringar.</br></br>Om domänkontrollanten firmware-versioner är olika och den gamla versionen av inbyggd programvara kan uppgraderas automatiskt, domänkontrollant ersättning logiken identifiera detta och när styrenheten har startat den inbyggda programvaran uppdateras automatiskt. |

Du måste ta bort en domänkontrollant modul om den har misslyckats. En eller båda controller moduler kan misslyckas, vilket kan resultera i en enda domänkontrollant ersättare eller ett dual-styrenheten ersättning. Ersättning procedurer och logiken bakom dem, finns i följande avsnitt:

* [Ersätta en enskild domänkontrollant](#replace-a-single-controller)
* [Ersätt både domänkontrollanter](#replace-both-controllers)
* [Ta bort en domänkontrollant](#remove-a-controller)
* [Infoga en domänkontrollant](#insert-a-controller)
* [Identifiera den aktiva styrenheten på enheten](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Innan du tar bort och ersätter en domänkontrollant kan granska säkerhetsinformation i [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Ersätta en enda domänkontrollant
Du måste ersätta en enda domänkontrollant när något av två domänkontrollanter på Microsoft Azure StorSimple-enheten har misslyckats, är fel eller saknas. 

### <a name="single-controller-replacement-logic"></a>Styrenhet ersättning logik
Du bör ta bort misslyckade domänkontrollant i en enda domänkontrollant ersättning. (Den återstående domänkontrollanten i enheten är den aktiva styrenheten.) När du infogar ersättning domänkontrollant sker följande åtgärder:

1. Ersättning av domänkontrollant startar direkt kommunikation med StorSimple-enheten.
2. En ögonblicksbild av den virtuella hårddisken (VHD) för den aktiva styrenheten kopieras på styrenheten för ersättning.
3. Ögonblicksbilden har ändrats så att när ersättning domänkontrollant startar från den här virtuella Hårddisken, det tolkas som en vänteläge domänkontrollant.
4. När ändringarna har slutförts startar ersättning domänkontrollant som vänteläge domänkontrollant.
5. När båda domänkontrollanterna kör är klustret online.

### <a name="single-controller-replacement-steps"></a>Styrenhet steg för ersättning
Utför följande steg om en av domänkontrollanterna i din Microsoft Azure StorSimple-enhet misslyckas. (Den andra styrenheten måste vara aktiv och körs. Om båda domänkontrollanter misslyckas eller fungera, gå till [dual-styrenheten ersättning steg](#dual-controller-replacement-steps).)

> [!NOTE]
> Det kan ta 30 – 45 minuter att starta om och återställa helt från styrenhet ersättning procedur-styrenhet. Den sammanlagda tiden för hela proceduren, är inklusive kopplar kablar, cirka 2 timmar.
> 
> 

#### <a name="to-remove-a-single-failed-controller-module"></a>Ta bort en modul misslyckades styrenhet
1. I Azure klassiska portal, gå till StorSimple Manager-tjänsten klickar du på den **enheter** fliken och klicka sedan på namnet på den enhet som du vill övervaka.
2. Gå till **Underhåll > maskinvarustatus**. Status för styrenhet 0 eller 1 för domänkontrollanten ska vara röd, vilket tyder på ett fel.
   
   > [!NOTE]
   > Misslyckade domänkontrollant i en enda domänkontrollant ersättning är alltid en standby-styrenhet.
   > 
   > 
3. Använd bild 1 och i följande tabell för att hitta modulen felaktig styrenhet.  
   
    ![Bakplan av primära Höljesmoduler](./media/storsimple-controller-replacement/IC740994.png)
   
    **Bild 1** tillbaka på StorSimple-enhet
   
   | Etikett | Beskrivning |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Styrenhet 0 |
   | 4 |Kontrollant 1 |
4. Ta bort alla anslutna nätverkskablarna från data hamnar på felaktig styrenhet. Om du använder en 8600 modell kan också ta bort SAS-kablar som ansluter styrenheten till EBOD-styrenhet.
5. Följ stegen i [ta bort en domänkontrollant](#remove-a-controller) att ta bort misslyckade domänkontrollant. 
6. Installera factory ersättning på samma plats som felaktig styrenhet har tagits bort. Detta utlöser styrenhet ersättning logik. Mer information finns i [enkel domänkontrollant ersättning logik](#single-controller-replacement-logic).
7. Medan styrenhet ersättning logiken fortskrider i bakgrunden, ansluta kablar. Var noga med för att ansluta alla kablar på samma sätt som de var anslutna innan du ersätter.
8. När domänkontrollanten har startats om kontrollerar du den **status domänkontrollanten** och **kluster status** i den klassiska Azure-portalen och kontrollera att kontrollanten är tillbaka till felfritt tillstånd och är i vänteläge.

> [!NOTE]
> Om du övervakar enheten via seriekonsolen kan du se flera omstarter medan styrenheten återställning från förfarandet som ersättning. När menyn för seriekonsolen visas vet att ersättningen har slutförts. Om inte visas menyn inom två timmar för att starta controller ersätter du [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).
>
> Starta uppdatering 4, du kan också använda cmdlet `Get-HCSControllerReplacementStatus` i Windows PowerShell-gränssnittet på enheten för att övervaka status för styrenheten ersättningsprocessen.
> 

## <a name="replace-both-controllers"></a>Ersätt både domänkontrollanter
När både domänkontrollanter på Microsoft Azure StorSimple-enheten har misslyckats, är felaktiga eller saknas, måste du ersätta båda domänkontrollanter. 

### <a name="dual-controller-replacement-logic"></a>Dubbla domänkontrollant ersättning logik
I en dubbel domänkontrollant ersättning du först ta bort både misslyckade domänkontrollanter och infoga ersättningar. När två ersättning domänkontrollanter infogas sker följande åtgärder:

1. Ersättning domänkontrollanten i uttag 0 kontrollerar du följande:
   
   1. Använder den aktuella versioner av inbyggd programvara och programvara?
   2. Det är en del av klustret?
   3. Peer-domänkontrollant som körs och är det klustrade?
      
      Om ingen av dessa villkor är SANT kontrollanten söker efter de senaste daglig säkerhetskopieringen (finns i den **nonDOMstorage** på enheten S). Styrenheten kopierar senaste ögonblicksbilden av den virtuella Hårddisken från säkerhetskopian.
2. Domänkontrollanten i uttag 0 använder ögonblicksbilden för att avbilda sig själv.
3. Domänkontrollanten i uttag 1 väntar under tiden för styrenhet 0 för att slutföra avbildning och starta.
4. När styrenhet 0 startas, identifierar kontrollant 1 klustret skapas av styrenhet 0, vilket utlöser styrenhet ersättning logik. Mer information finns i [enkel domänkontrollant ersättning logik](#single-controller-replacement-logic).
5. Därefter kan både domänkontrollanter ska köras och klustret kommer att försättas online.

> [!IMPORTANT]
> Efter en dubbel domänkontrollant ersättning när StorSimple-enheten har konfigurerats, är det viktigt att du utföra en manuell säkerhetskopiering för enheten. Dagliga säkerhetskopior för konfiguration av enheter aktiveras inte förrän efter 24 timmar har löpt ut. Arbeta med [Microsoft-supporten](storsimple-contact-microsoft-support.md) att göra en manuell säkerhetskopiering av din enhet.
> 
> 

### <a name="dual-controller-replacement-steps"></a>Dubbla domänkontrollant ersättning steg
Det här arbetsflödet krävs när båda av domänkontrollanter i din Microsoft Azure StorSimple-enhet har misslyckats. Detta kan inträffa i ett datacenter där kylning systemet slutar fungera och därför båda domänkontrollanterna misslyckas inom en kort tidsperiod. Beroende på om StorSimple-enheten är avstängd eller på och om du använder en 8600 eller en 8100-modell, en annan uppsättning steg krävs.

> [!IMPORTANT]
> Det kan ta 45 minuter till 1 timme att starta om och återställa helt från en dubbel domänkontrollant ersättning procedur-styrenhet. Den sammanlagda tiden för hela proceduren, är inklusive kopplar kablar, ungefär 2,5 timmar.
> 
> 

#### <a name="to-replace-both-controller-modules"></a>Ersätta båda styrenhet-moduler
1. Om enheten är inaktiverad, hoppa över det här steget och gå vidare till nästa steg. Om enheten är påslagen, stänga av enheten.
   
   1. Om du använder en 8600-modell, stänga av primära höljet först och sedan inaktivera EBOD höljet.
   2. Vänta tills enheten har avslutats helt. Alla indikatorer på baksidan enheten ska vara inaktiverat.
2. Ta bort alla nätverkskablarna som ansluts till dataportar. Om du använder en 8600 modell kan du också ta bort SAS-kablar som ansluter primära höljet till EBOD höljet.
3. Ta bort både domänkontrollanter från StorSimple-enhet. Mer information finns i [ta bort en domänkontrollant](#remove-a-controller).
4. Infoga factory ersättning för styrenhet 0 först och infoga Controller 1. Mer information finns i [infoga en domänkontrollant](#insert-a-controller). Detta utlöser dubbla domänkontrollant ersättning logik. Mer information finns i [dual-styrenheten ersättning logik](#dual-controller-replacement-logic).
5. Medan controller ersättning logiken fortskrider i bakgrunden, ansluta kablar. Var noga med för att ansluta alla kablar på samma sätt som de var anslutna innan du ersätter. Avsnittet detaljerade instruktioner för din modell kabeln din enhet i [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).
6. Aktivera på StorSimple-enheten. Om du använder en 8600-modellen:
   
   1. Se till att EBOD höljet är aktiverat på första.
   2. Vänta tills EBOD höljet körs.
   3. Aktivera primära höljet.
   4. När den första enheten startas om och är i felfritt tillstånd, körs i systemet.
      
      > [!NOTE]
      > Om du övervakar enheten via seriekonsolen kan du se flera omstarter medan styrenheten återställning från förfarandet som ersättning. När menyn för seriekonsolen visas vet att ersättningen har slutförts. Om inte visas menyn inom 2,5 timmar för att starta controller ersätter du [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md).
      > 
      > 

## <a name="remove-a-controller"></a>Ta bort en domänkontrollant
Använd följande procedur för att ta bort en felaktig Styrenhetsmodul från din StorSimple-enhet.

> [!NOTE]
> Följande bilder är för styrenhet 0. Kontrollant 1 skulle dessa vara omvänd.
> 
> 

#### <a name="to-remove-a-controller-module"></a>Ta bort en domänkontrollant-modul
1. Tag i modulen spärren mellan USB och pekfingret.
2. Försiktigt klämma dina USB och pekfingret tillsammans för att frigöra controller spärren.
   
    ![Släppa controller spärren](./media/storsimple-controller-replacement/IC741047.png)
   
    **Bild 2** frisläppning controller spärren
3. Använd låset som en referens till domänkontrollanten utanför chassit bild.
   
    ![Glidande controller out-of-chassi](./media/storsimple-controller-replacement/IC741048.png)
   
    **Bild 3** glidande domänkontrollant utanför chassit

## <a name="insert-a-controller"></a>Infoga en domänkontrollant
Använd följande procedur för att installera en modul levererats från fabriken domänkontrollant när du tar bort en felaktig modul från din StorSimple-enhet.

#### <a name="to-install-a-controller-module"></a>Att installera en domänkontrollant-modul
1. Kontrollera om det finns skador på gränssnittet kopplingar. Installera inte modulen om någon PIN-koder för anslutningen är skadad eller böjda.
2. Dra modulen domänkontrollant i chassit medan låset frigörs fullständigt. 
   
    ![Glidande domänkontrollant i chassi](./media/storsimple-controller-replacement/IC741053.png)
   
    **Bild 4** glidande domänkontrollant i chassit
3. Börja stänger låset medan fortsätter att skicka modulen domänkontrollant i chassit med modulen controller infogas. Låset anlitar guide styrenheten till rätt plats.
   
    ![Stänger controller spärren](./media/storsimple-controller-replacement/IC741054.png)
   
    **Bild 5** stänger controller spärren
4. Du är klar när låset fästs på plats. Den **OK** Indikator bör nu vara på.  
   
   > [!NOTE]
   > Det kan ta upp till 5 minuter för styrenheten och Indikator för att aktivera.
   > 
   > 
5. Kontrollera att ersättningen har utförts i den klassiska Azure portalen, gå till **enheter** > **Underhåll** > **maskinvarustatus**, och Se till att både styrenhet 0 och kontrollant 1 är felfri (statusen är grön).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiera den aktiva styrenheten på enheten
Det finns många situationer, till exempel första gången enheten registreringen eller domänkontrollant ersättning, som kräver att du att hitta den aktiva styrenheten på StorSimple-enhet. Den aktiva styrenheten bearbetar alla disk inbyggd programvara och nätverk åtgärder. Du kan använda någon av följande metoder för att identifiera den aktiva styrenheten:

* [Använd den klassiska Azure-portalen för att identifiera den aktiva styrenheten](#use-the-azure-classic-portal-to-identify-the-active-controller)
* [Använda Windows PowerShell för StorSimple för att identifiera den aktiva styrenheten](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Kontrollera den fysiska enheten för att identifiera den aktiva styrenheten](#check-the-physical-device-to-identify-the-active-controller)

Var och en av de här procedurerna beskrivs nedan.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Använd den klassiska Azure-portalen för att identifiera den aktiva styrenheten
I den klassiska Azure-portalen går du till **enheter** > **Underhåll**, och bläddra till den **domänkontrollanter** avsnitt. Här kan du kontrollera vilka domänkontrollanten är aktiv.

![Identifiera aktiva styrenheten i klassiska Azure-portalen](./media/storsimple-controller-replacement/IC752072.png)

**Bild 6** klassiska Azure-portalen med den aktiva styrenheten

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Använda Windows PowerShell för StorSimple för att identifiera den aktiva styrenheten
När du har åtkomst till din enhet via seriekonsolen visas Banderollmeddelandet. Banderollmeddelandet innehåller grundläggande enhetsinformation som modellen, namn, version installerad programvara och status för den domänkontrollant som du ansluter till. Följande bild visar ett exempel på Banderollmeddelandet:

![Seriell Banderollmeddelandet](./media/storsimple-controller-replacement/IC741098.png)

**Bild 7** banderoll meddelande visar styrenhet 0 som aktiv

Du kan använda Banderollmeddelandet för att avgöra om den domänkontrollant som du är ansluten till är aktiva eller passiva.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Kontrollera den fysiska enheten för att identifiera den aktiva styrenheten
Att identifiera den aktiva styrenheten på enheten, leta upp blå VÄGLEDS ovan DATA 5 port på baksidan av primära höljet.

Om denna Indikator blinkar på domänkontrollanten är aktiv och andra domänkontrollanter är i vänteläge. Använd följande diagram och tabell som hjälp.

![Enheten primära höljet bakplan med dataportar](./media/storsimple-controller-replacement/IC741055.png)

**Figur 8** baksidan primär enhet med dataportar och övervakning indikatorer

| Etikett | Beskrivning |
|:--- |:--- |
| 1-6 |DATA 0 – 5 nätverksportar |
| 7 |Blå Indikator |

## <a name="next-steps"></a>Nästa steg
Lär dig mer om [ersättning av StorSimple maskinvara komponenten](storsimple-hardware-component-replacement.md).

