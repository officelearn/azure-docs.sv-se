---
title: Återställa en volym från säkerhetskopiering på en StorSimple 8000-serie | Microsoft-dokument
description: I artikeln beskrivs hur du ansÃ¤nder Ã¤ nã¤tts i storsimple-tjänsthanterarens servicerÃ¤llningskatalog fÃ¥ller en StorSimple-volym frÃ¥n en säkerhetskopia.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 6a2e022697ced90d968075b7a4abe4163be7a539
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723398"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Återställa en StorSimple-volym från en säkerhetskopia

## <a name="overview"></a>Översikt

Den här självstudien beskriver återställningen som utförs på en StorSimple 8000-serieenhet med hjälp av en befintlig säkerhetskopia. Använd **bladet Säkerhetskopieringskatalog** för att återställa en volym från en lokal säkerhetskopiering eller molnsäkerhetskopiering. Katalogbladet **För säkerhetskopiering** visar alla säkerhetskopior som skapas när manuella eller automatiska säkerhetskopior görs. Återställningen från en säkerhetskopia visar volymen online direkt medan data hämtas i bakgrunden.

En alternativ metod för att starta återställningen är att gå till **Enheter > [Enheten] > volymer**. I bladet **Volymer** väljer du en volym, högerklickar för att anropa snabbmenyn och väljer sedan **Återställ**.

## <a name="before-you-restore"></a>Innan du återställer

Innan du påbörjar en återställning bör du läsa följande varningar:

* **Du måste koppla från volymen** – Koppla volymen från värden och enheten innan du påbörjar återställningen. Även om återställningen automatiskt för volymen online på enheten, måste du manuellt ansluta enheten på värden. Du kan ansluta volymen till värden så fort volymen är online på enheten. (Du behöver inte vänta tills återställningen är klar.) Om du vill ha procedurer går du till [Ta en volym offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volymtyp efter återställning** – Borttagna volymer återställs baserat på typen i ögonblicksbilden. det vill än volymer som fästas lokalt återställs som lokalt fästa volymer och volymer som har nivåindelat återställs som nivåindelade volymer.

    För befintliga volymer åsidosätter den aktuella användningstypen för volymen den typ som lagras i ögonblicksbilden. Om du till exempel återställer en volym från en ögonblicksbild som togs när volymtypen nivåindelades och den volymtypen nu är lokalt fäst (på grund av en konverteringsåtgärd som utfördes), återställs volymen som en lokalt fäst volym. På samma sätt, om en befintlig lokalt fäst volym expanderades och därefter återställdes från en äldre ögonblicksbild som togs när volymen var mindre, behåller den återställda volymen den aktuella utökade storleken.

    Du kan inte konvertera en volym från en nivåindelad volym till en lokalt fäst volym eller från en lokalt fäst volym till en nivåindelad volym medan volymen återställs. Vänta tills återställningen är klar och sedan kan du konvertera volymen till en annan typ. Information om hur du konverterar en volym finns i [Ändra volymtyp](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Volymstorleken återspeglas i den återställda volymen** – Det här är en viktig faktor om du återställer en lokalt fäst volym som har tagits bort (eftersom lokalt fästa volymer är helt etablerade). Kontrollera att du har tillräckligt med utrymme innan du försöker återställa en lokalt fäst volym som tidigare har tagits bort.

* **Du kan inte expandera en volym medan den återställs** – Vänta tills återställningen är klar innan du försöker expandera volymen. Information om hur du expanderar en volym finns i [Ändra en volym](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Du kan utföra en säkerhetskopia medan du återställer en lokal volym** – För procedurer gå till [Använd Tjänsten StorSimple Device Manager för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

* **Du kan avbryta en återställningsåtgärd** – Om du avbryter återställningsjobbet återställs volymen tillbaka till det tillstånd som den befann sig i innan du påbörjade återställningen. Om du vill ha procedurer går du till [Avbryt ett jobb](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hur återställer arbete

För enheter som kör uppdatering 4 eller senare implementeras en heatmap-baserad återställning. När värden begär att få åtkomst till data når enheten spåras dessa begäranden och en värmekarta skapas. Hög begäranhetsfrekvens resulterar i datasegment med högre värme medan lägre begäranhetsfrekvens översätts till bitar med lägre värme. Du måste komma åt data minst två gånger för att markeras som _heta_. En fil som har ändrats markeras också som _het_. När du har initierat återställningen sker proaktiv återfuktning av data baserat på värmekartan. För tidigare versioner än uppdatering 4 hämtas data under återställningen endast baserat på åtkomst.

Följande varningar gäller för heatmap-baserade återställningar:

* Heatmap-spårning är endast aktiverat för nivåindelade volymer och lokalt fästa volymer stöds inte.

* Värmekartabaserad återställning stöds inte vid kloning av en volym till en annan enhet. 

* Om det finns en återställning på plats och en lokal ögonblicksbild för volymen som ska återställas finns på enheten, så återfuktar vi inte (eftersom data redan är tillgängliga lokalt). 

* När du återställer initieras som standard rehydreringsjobb som proaktivt återfuktar data baserat på värmekartan. 

I uppdatering 4 kan Windows PowerShell-cmdlets användas för att fråga köra rehydreringsjobb, avbryta ett rehydreringsjobb och få status för rehydreringsjobbet.

* `Get-HcsRehydrationJob`- Den här cmdleten får status som rehydreringsjobbet. Ett enda rehydreringsjobb utlöses för en volym.

* `Set-HcsRehydrationJob`- Denna cmdlet kan du pausa, stoppa, återuppta rehydrering jobb, när rehydrering pågår.

Mer information om rehydrerings cmdlets finns i [Windows PowerShell cmdlet reference for StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Med automatisk rehydrering förväntas vanligtvis högre övergående läsprestanda. Den faktiska omfattningen av förbättringar beror på olika faktorer som åtkomstmönster, dataomsättning och datatyp. 

Om du vill avbryta ett rehydreringsjobb kan du använda PowerShell-cmdleten. Om du vill inaktivera rehydreringsjobb permanent för alla framtida återställningar [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Så här använder du säkerhetskopieringskatalogen

**Bladet Säkerhetskopieringskatalog** innehåller en fråga som hjälper dig att begränsa valet av säkerhetskopia. Du kan filtrera de säkerhetskopieringsuppsättningar som hämtas baserat på följande parametrar:

* **Tidsintervall** – Datum och tidsintervall när säkerhetskopian skapades.
* **Enhet** – Enheten där säkerhetskopian skapades.
* **Säkerhetskopieringsprincip** eller **Volym** – Säkerhetskopieringsprincipen eller volymen som är associerad med den här säkerhetskopian.

De filtrerade säkerhetskopieringsuppsättningarna tabuleras sedan baserat på följande attribut:

* **Namn** – Namnet på säkerhetskopieringsprincipen eller volymen som är associerad med säkerhetskopian.
* **Typ** – Säkerhetskopieringsuppsättningar kan vara lokala ögonblicksbilder eller ögonblicksbilder av molnet. En lokal ögonblicksbild är en säkerhetskopia av alla volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet refererar till säkerhetskopiering av volymdata som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst, medan ögonblicksbilder i molnet väljs för dataåtersåterkoppling.
* **Storlek** – Den faktiska storleken på säkerhetskopian.
* **Skapad på** – Datum och tid då säkerhetskopiorna skapades. 
* **Volymer** - Antalet volymer som är associerade med säkerhetskopian.
* **Initierad** – Säkerhetskopiorna kan initieras automatiskt enligt ett schema eller manuellt av en användare. (Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopior. Du kan också använda alternativet **Ta säkerhetskopiera** för att göra en interaktiv säkerhetskopiering eller säkerhetskopiering på begäran.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Så här återställer du StorSimple-volymen från en säkerhetskopia

Du kan använda bladet **Säkerhetskopieringskatalog** för att återställa StorSimple-volymen från en viss säkerhetskopia. Tänk dock på att återställa en volym kommer att återställa volymen till det tillstånd den var i när säkerhetskopian togs. Alla data som har lagts till efter säkerhetskopieringen kommer att gå förlorade.

> [!WARNING]
> Om du återställer från en säkerhetskopia ersätts de befintliga volymerna från säkerhetskopian. Detta kan orsaka förlust av data som skrevs efter säkerhetskopieringen togs.


### <a name="to-restore-your-volume"></a>Så här återställer du volymen
1. Gå till tjänsten StorSimple Device Manager och klicka sedan på **Säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia enligt följande:
   
   1. Ange tidsintervallet.
   2. Välj lämplig enhet.
   3. I listrutan väljer du volym- eller säkerhetskopieringsprincipen för den säkerhetskopia som du vill välja.
   4. Klicka på **Använd** för att köra den här frågan.

      De säkerhetskopior som är associerade med den valda volym- eller säkerhetskopieringsprincipen ska visas i listan över säkerhetskopior.
   
      ![Lista över säkerhetskopior](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expandera säkerhetskopian för att visa de associerade volymerna. Dessa volymer måste tas offline på värden och enheten innan du kan återställa dem. Öppna volymerna på **enhetens volymblad** och följ sedan stegen i [Koppla från volymen](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) för att koppla från dem.
   
   > [!IMPORTANT]
   > Se till att du har tagit volymerna offline på värden först, innan du tar volymerna offline på enheten. Om du inte tar volymerna offline på värden kan det leda till skadade data.
   
4. Navigera tillbaka till fliken **Säkerhetskopieringskatalog** och välj en säkerhetskopia. Högerklicka och välj sedan **Återställ**på snabbmenyn .

    ![Lista över säkerhetskopior](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Du uppmanas att bekräfta. Granska återställningsinformationen och markera sedan kryssrutan Bekräftelse.
   
    ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klicka på **Återställ**. Då initieras ett återställningsjobb som du kan visa genom att öppna sidan **Jobb.**

   ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. När återställningen är klar kontrollerar du att innehållet i dina volymer ersätts av volymer från säkerhetskopian.


## <a name="if-the-restore-fails"></a>Om återställningen misslyckas

Du får en avisering om återställningen misslyckas av någon anledning. Om detta inträffar uppdaterar du säkerhetskopieringslistan för att kontrollera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig och du återställer från molnet kan anslutningsproblem orsaka problemet.

Slutför återställningen genom att koppla från volymen på värden och försöka återställa återställningen igen. Observera att alla ändringar av volymdata som utfördes under återställningsprocessen kommer att gå förlorade.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hanterar StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

