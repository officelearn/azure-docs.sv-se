---
title: StorSimple redundans och disaster recovery | Microsoft Docs
description: "Lär dig mer om att växla över din StorSimple-enhet till sig själv, en annan fysisk enhet eller en virtuell enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 5751598e-49c8-42b3-8121-fea5857a7d83
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: alkohli
ms.openlocfilehash: bf92ffdb16b86c4033cc96ae2abb060d90f9505e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-device"></a>Redundans och disaster recovery för din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här självstudiekursen beskriver de steg som krävs för att växla en StorSimple-enhet vid en katastrof. En redundansväxling kan du migrera data från en källenhet i datacenter till ett annat fysiskt eller även en virtuell enhet som finns i samma eller en annan geografisk plats. 

Katastrofåterställning (DR) styrd via funktionen med redundans i enheten och initieras från den **enheter** sidan. Den här sidan tabulates alla StorSimple-enheter som är anslutna till din StorSimple Manager-tjänst. Eget namn, status, etablerad och maximal kapacitet, typ och modell visas för varje enhet.

![Enheter-sidan](./media/storsimple-device-failover-disaster-recovery/IC740972.png)

Riktlinjerna i den här kursen gäller StorSimple fysiska och virtuella enheter över alla programvaruversioner.

## <a name="disaster-recovery-dr-and-device-failover"></a>Katastrofåterställning (DR) och växling vid fel för enheten
I en (DR) katastrofåterställning, den primära enheten slutar fungera. I så fall kan du flytta molndata som är associerade med misslyckade enheten till en annan enhet med hjälp av användarens primära enhet som den *källa* och ange en annan enhet som den *mål*. Du kan välja en eller flera volymbehållare att migrera till målenheten. Den här processen kallas den *redundans*. 

Under växling vid fel, volymbehållarna från källan ändra ägarskap och överförs till målenheten. När volymbehållarna ändra ägarskap, bort dessa från källan. När borttagningen är klar misslyckades kan sedan målenheten tillbaka.

Vanligtvis följande DR, den senaste säkerhetskopian används för att återställa data till målenheten. Om det finns flera principer för säkerhetskopiering för samma volym, sedan säkerhetskopieringsprincip med det största antalet volymer hämtar plockats och den senaste säkerhetskopian från principen används för att återställa data på målenheten.

Som exempel, om det finns två säkerhetskopieringsprinciper (en standard och en anpassad) *defaultPol*, *customPol* med följande information:

* *defaultPol* : en volym *vol1*, kör daglig början på 10:30 PM.
* *customPol* : fyra volymer *vol1*, *vol2*, *vol3*, *vol4*, kör daglig början på 10:00 PM.

I det här fallet *customPol* kommer att användas som innehåller flera volymer och vi prioriterar för kraschkonsekvens. Den senaste säkerhetskopian från den här principen används för att återställa data.

## <a name="considerations-for-device-failover"></a>Överväganden för växling vid fel för enheten
Vid en katastrof kan du välja att växla över din StorSimple-enhet:

* Till en fysisk enhet 
* Till sig själv
* Till en virtuell enhet

För varje enhet redundans, Tänk på följande:

* Kraven för Katastrofåterställning är att alla volymer i volymbehållarna är offline och volymbehållarna har en associerad ögonblicksbild i molnet. 
* De tillgängliga målenheterna för Katastrofåterställning är enheter som har tillräckligt med utrymme för de valda volymbehållarna. 
* De enheter som är anslutna till din tjänst men inte uppfyller villkoren för tillräckligt med utrymme är inte tillgänglig som målenheter.
* Efter en DR för en begränsad tid data access-prestanda kan påverkas avsevärt som enheten måste du komma åt data från molnet och lagras lokalt.

#### <a name="device-failover-across-software-versions"></a>Enheten redundans över programvaruversioner
En StorSimple Manager-tjänsten i en distribution kan ha flera enheter, både fysiska och virtuella, alla kör olika versioner. Beroende på programvaruversionen kan volymtyper på enheterna också vara olika. Till exempel en enheten som kör uppdatering 2 eller högre skulle ha lokalt Fäst och nivåindelade volymer (med arkivering som en del av skikt). Å andra sidan kan ha nivåer en före uppdatering 2-enhet och arkivering volymer. 

Använd följande tabell för att avgöra om kan du växla över till en annan enhet som kör en annan programvara och beteendet för volymtyper under Katastrofåterställning.

| Växla över från | Tillåten för den fysiska enheten | Tillåtna för virtuella enheten |
| --- | --- | --- |
| Uppdatering 2 för att före uppdatering 1 (version 0.1, 0,2, 0.3) |Nej |Nej |
| Uppdatering 2 för att uppdatera 1 (1, 1.1, 1.2) |Ja <br></br>Om du använder lokalt Fäst eller nivåer volymer eller en blandning av två, misslyckades volymerna alltid över eftersom nivåer. |Ja<br></br>Om du använder lokalt Fäst volymer, kunde dessa över nivåer. |
| Uppdatering 2 för uppdatering 2 (senare version) |Ja<br></br>Om du använder lokalt fästa eller nivåindelade volymer eller en blandning av två har volymer alltid redundansväxlats som den första volymtypen; nivåer som nivåindelade och lokalt Fäst lokalt Fäst. |Ja<br></br>Om du använder lokalt Fäst volymer, kunde dessa över nivåer. |

#### <a name="partial-failover-across-software-versions"></a>Partiell redundans över programvaruversioner
Följ dessa riktlinjer om du vill utföra en partiell redundans med en StorSimple-källenheten körs före uppdatering 1 för ett mål som kör uppdatering 1 eller senare. 

| Partiell växling från | Tillåten för den fysiska enheten | Tillåtna för virtuella enheten |
| --- | --- | --- |
| Före uppdatering 1 (version 0.1, 0,2, 0.3) Update 1 eller senare |Ja, se nedan för bästa praxis-tips. |Ja, se nedan för bästa praxis-tips. |

> [!TIP]
> Det uppstod molnet metadata och data format ändras uppdatering 1 och senare versioner. Vi rekommenderar därför inte en partiell växling från före uppdatering 1 till uppdatering 1 eller senare versioner. Om du behöver utföra en partiell redundans rekommenderar vi att du först installera uppdatering 1 eller senare på båda enheterna (källan och målet) och fortsätt sedan med växling vid fel. 
> 
> 

## <a name="fail-over-to-another-physical-device"></a>Växla över till en annan fysisk enhet
Utför följande steg för att återställa enheten till en fysisk enhet som mål.

1. Kontrollera att volymbehållaren som du vill växla över associeras molnögonblicksbilder.
2. På den **enheter** klickar du på den **Volymbehållare** fliken.
3. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym och klicka på **ta Offline** att kopplas ifrån volymen. Upprepa proceduren för alla volymer i volymbehållaren.
4. Upprepa det föregående steget för alla volymbehållare som du vill växla över till en annan enhet.
5. På den **enheter** klickar du på **redundans**.
6. I guiden som öppnas, under **Välj volymbehållare att växla över**:
   
   1. Välj volymbehållarna som du vill växla över i listan över volymbehållare.
      **Volymbehållare med associerade molnögonblicksbilder och offline volymer visas.**
   2. Under **välja en målenhet** för volymer i de valda behållarna, väljer du en målenhet från listan över tillgängliga enheter. Endast de enheter som har tillgänglig kapacitet visas i den nedrullningsbara listan.
   3. Slutligen granska alla inställningar för växling vid fel under **bekräfta redundans**. Klicka på kryssikonen ![kryssikonen](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
7. Skapas ett jobb för växling vid fel som kan övervakas den **jobb** sidan. Om volymbehållaren som du redundansväxlade har lokala volymer, kommer du se enskilda återställningsjobb för varje lokal volym (inte för nivåindelade volymer) i behållaren. Dessa jobb kan ta tid för att slutföra återställningen. Det är troligt att beställningsjobbet kan slutföra tidigare. Observera att dessa volymer måste lokala garantier endast när återställningsjobb har slutförts. När redundansväxlingen är klar går du till den **enheter** sidan.                                            
   
   1. Välj den enhet som användes som målenhet för failover-processen.
   2. Gå till den **Volymbehållare** sidan. Alla volymbehållare tillsammans med volymer från den gamla enheten ska visas.

## <a name="failover-using-a-single-device"></a>Redundans med en enskild enhet
Utför följande steg om du bara har en enda enhet och behöver utföra en växling vid fel.

1. Skapa molnögonblicksbilder av alla volymer på din enhet.
2. Återställa enheten till fabriksinställningarna. Följ de detaljerade anvisningarna i [hur du återställer en StorSimple-enhet till fabriksinställningarna](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Konfigurera din enhet och registrera den igen med din StorSimple Manager-tjänst.
4. På den **enheter** sidan gamla enheten ska visa **Offline**. Nyligen registrerade enheten ska visa **Online**.
5. Slutför den lägsta konfigurationen av enheten först för den nya enheten. 
   
   > [!IMPORTANT]
   > **Om den lägsta konfigurationen inte har slutförts först misslyckas din DR på grund av ett fel i den aktuella implementeringen. Problemet korrigeras i en senare version.**
   > 
   > 
6. Välj den gamla enheten (offline status) och klicka på **redundans**. Växla över den här enheten i guiden som visas och ange målenheten som nyligen registrerad enhet. Detaljerade anvisningar finns i [växla över till en annan fysisk enhet](#fail-over-to-another-physical-device).
7. En enhet återställningsjobbet skapas att du kan övervaka från den **jobb** sidan.
8. När jobbet har slutförts, åtkomst till den nya enheten och navigera till den **Volymbehållare** sidan. Alla volymbehållarna från den gamla enheten ska nu migreras till den nya enheten.

## <a name="fail-over-to-a-storsimple-virtual-device"></a>Växla över till en virtuell StorSimple-enhet
Du måste ha en StorSimple virtuell enhet skapas och konfigureras innan du kör den här proceduren. Om du kör uppdatering 2, Överväg att använda en den virtuella enheten 8020 för Katastrofåterställning som har 64 TB och använder Premium-lagring. 

Utför följande steg för att återställa enheten till en mål virtuella StorSimple-enheten.

1. Kontrollera att volymbehållaren som du vill växla över associeras molnögonblicksbilder.
2. På den **enheter** klickar du på den **Volymbehållare** fliken.
3. Välj en volymbehållare som du vill växla över till en annan enhet. Klicka på volymbehållare om du vill visa en lista över volymer i den här behållaren. Välj en volym och klicka på **ta Offline** att kopplas ifrån volymen. Upprepa proceduren för alla volymer i volymbehållaren.
4. Upprepa det föregående steget för alla volymbehållare som du vill växla över till en annan enhet.
5. På den **enheter** klickar du på **redundans**.
6. I guiden som öppnas, under **Välj volymbehållare till redundans**, enligt följande:
   
    a. Välj volymbehållarna som du vill växla över i listan över volymbehållare.
   
    **Volymbehållare med associerade molnögonblicksbilder och offline volymer visas.**
   
    b. Under **välja en målenhet för volymer i de valda behållarna**, Välj den virtuella StorSimple-enheten från listan över tillgängliga enheter. **Endast de enheter som har tillräcklig kapacitet för visas i den nedrullningsbara listan.**  
7. Slutligen granska alla inställningar för växling vid fel under **bekräfta redundans**. Klicka på kryssikonen ![kryssikonen](./media/storsimple-device-failover-disaster-recovery/IC740895.png).
8. När redundansväxlingen är klar går du till den **enheter** sidan.
   
    a. Välj den virtuella enheten StorSimple som användes som målenhet för failover-processen.
   
    b. Gå till den **Volymbehållare** sidan. Alla volymbehållare tillsammans med volymer från den gamla enheten bör nu visas.

![Video tillgänglig](./media/storsimple-device-failover-disaster-recovery/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du kan återställa en misslyckad över fysiska enheten till en virtuell enhet i molnet, klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-and-disaster-recovery/).

## <a name="failback"></a>Återställning efter fel
För uppdatering 3 och senare versioner stöd StorSimple också för återställning efter fel. När redundansväxlingen är klar, utförs följande åtgärder:

* Volymbehållarna har redundansväxlats rensas från källan.
* Ett bakgrundsjobb per volymbehållare (redundansväxlats) initieras på källan. Om du försöker återställning efter fel medan jobbet pågår, visas ett meddelande om detta. Du måste vänta tills jobbet är klart att starta återställningen. 
  
    Tid att slutföra borttagningen av volymbehållare är beroende av olika faktorer som mängden data, åldern på data, antal säkerhetskopior och den tillgängliga bandbredden för åtgärden. Om du planerar att testa redundans/återställning efter fel, rekommenderar vi att du testar volymbehållare med mindre data (GB). I de flesta fall kan du starta återställningen 24 timmar efter växling vid fel är klar. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
FRÅGOR. **Vad händer om DR misslyckas eller har lyckats delvis?**

A. Om DR misslyckas, rekommenderar vi att du försöker igen. Den andra gången, DR vet vad alla gjordes och när processen har stoppats första gången. DR-processen startar från den tidpunkten och framåt. 

FRÅGOR. **Kan jag ta bort en enhet när enheten för växling vid fel pågår?**

A. Du kan inte ta bort en enhet medan en DR pågår. Du kan bara ta bort din enhet när ar har slutförts.

FRÅGOR.    **När startar skräpinsamling på källenheten så att den lokala data på källenheten har tagits bort?**

A. Skräpinsamling aktiveras på källenheten endast när enheten har rensats helt. Rensningen innehåller Rensa objekt som har redundansväxlats från källan till exempel volymer, säkerhetskopieobjekt (inte data), volymbehållare och principer.

FRÅGOR. **Vad händer om det inte går att ta bort jobbet som är associerade med volymbehållare i källan?**

A.  Om jobbet Ta bort misslyckas måste du manuellt starta borttagningen av volymbehållarna. I den **enheter** väljer enheten källa och klicka på **volymbehållare**. Välj volymbehållarna som du inte över och längst ned på sidan, klicka på **ta bort**. När du har tagit bort alla de över volymbehållare på källenhet, kan du starta återställningen.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Katastrofåterställning för verksamhetskontinuitet (BCDR)
En business continuity (BCDR) katastrofåterställning inträffar när hela Azure-datacentret slutar att fungera. Detta kan påverka din StorSimple Manager-tjänsten och de associera StorSimple-enheterna.

Om det finns StorSimple-enheter som registrerats precis innan en katastrof inträffade, kan dessa StorSimple-enheter måste genomgå en fabriksåterställning. Efter katastrofåterställning visas StorSimple-enhet som offline. StorSimple-enheten måste tas bort från portalen och en fabriksåterställning ska göras, följt av en ny registrering.

## <a name="next-steps"></a>Nästa steg
* När du har utfört en växling vid fel, kan du behöva [inaktivera eller ta bort din StorSimple-enhet](storsimple-deactivate-and-delete-device.md).
* Information om hur du använder StorSimple Manager-tjänsten finns på [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

