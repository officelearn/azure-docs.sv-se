---
title: Återställa en volym från en säkerhetskopia på en StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder StorSimple Device Manager-tjänsten säkerhetskopieringskatalogen för att återställa en StorSimple-volym från en säkerhetskopia.
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
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082814"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Återställa en StorSimple-volym från en säkerhetskopia

## <a name="overview"></a>Översikt

Den här självstudien beskrivs återställningen utförs på en enhet i StorSimple 8000-serien med en befintlig säkerhetskopia. Använd den **säkerhetskopieringskatalog** bladet för att återställa en volym från en lokal eller säkerhetskopiering i molnet. Den **säkerhetskopieringskatalog** bladet visar alla säkerhetskopior som skapas när manuella eller automatiska säkerhetskopior tas. Återställningen från en säkerhetskopia ger volymen är online direkt, medan data hämtas i bakgrunden.

En alternativ metod för att starta återställning är att gå till **enheter > [enheten] > volymer**. I den **volymer** bladet väljer du en volym, högerklicka för att öppna snabbmenyn och välj sedan **återställa**.

## <a name="before-you-restore"></a>Innan du återställer

Innan du startar en återställning kan du granska följande varningar:

* **Du måste koppla från volymen** – ta frånkoppla volymen på värden och på enheten innan du startar återställningen igen. Även om återställningen tar automatiskt volymen är online på enheten, måste du manuellt Anslut enheten på värden. Du kan använda volymen är online på värden när volymen är online på enheten. (Du behöver inte vänta tills återställningen har slutförts.) Mer information om procedurer går du till [koppla från en volym](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volymtyp efter återställning** – borttagna volymer återställs beroende på vilken i ögonblicksbilden, det vill säga volymer som fästs lokalt återställs som lokalt fixerade volymer och volymer som har nivåindelade återställs som nivåindelade volymer.

    För befintliga volymer åsidosätter aktuella användningstyp volymens den typ som sparades i ögonblicksbilden. Till exempel om du återställer en volym från en ögonblicksbild som vidtogs när typ av volym har nivåer och volymtyp är nu Fäst lokalt (på grund av en konvertering som utfördes) kommer sedan volymen att återställas som en lokalt Fäst volym. Om en befintlig lokalt Fäst volym har utökats och därefter återställts från en äldre ögonblicksbilden när volymen var mindre, på samma sätt kan behåller den återställda volymen den aktuella utökade storleken.

    Du kan inte konvertera en volym från en nivåindelad volym till en lokalt Fäst volym eller från en lokalt Fäst volym till en nivåindelad volym när volymen återställs. Vänta tills återställningen är klar och du kan sedan konvertera volymen till en annan typ. Information om hur du konverterar en volym, går du till [ändra volymtyp](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Volymens storlek återspeglas i återställda volymen** – detta är viktigt om du återställer en lokalt Fäst volym som har tagits bort (eftersom lokalt fixerade volymer är helt etablerad). Se till att du har tillräckligt med utrymme innan du försöker återställa en lokalt Fäst volym som tidigare har tagits bort.

* **Du kan inte utöka en volym när den återställs** – vänta tills återställningen har slutförts innan du försöker att utöka volymen. Information om hur du utökar en volym, går du till [ändra en volym](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Du kan utföra en säkerhetskopiering när du återställer en lokal volym** – för procedurer går du till [använda StorSimple Device Manager-tjänsten för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

* **Du kan avbryta en återställningsåtgärd** – om du avbryter återställningsjobbet, och sedan volymen kommer att återställas till det tillstånd som innan du startade återställningen igen. Mer information om procedurer går du till [avbryta ett jobb](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hur återställs arbete

För enheter som kör uppdatering 4 eller senare kan har en termisk karta-baserad återställning implementerats. Som värden begär att få åtkomst till data nå din enhet, begärandena spåras och en termisk karta skapas. Hög begärandehastighet resulterar i datasegment med högre termisk medan lägre begärandehastighet översätts till segment med lägre termisk. Du måste ansluta till data minst två gånger för att markeras som _frekvent_. En fil som ändras markeras också _frekvent_. När du startar återställningen sker proaktiv hydrering av data baserat på den termiska kartan. För versioner före uppdatering 4 data laddas ned under återställning som baseras på åtkomst endast.

Följande villkor gäller för den termiska kartan-baserade återställningar:

* Termisk karta spårning aktiveras endast för nivåindelade volymer och lokalt fixerade volymer stöds inte.

* Termisk karta-baserad återställning stöds inte när du klonar en volym till en annan enhet. 

* Om det finns en återställning på plats och en lokal ögonblicksbild för volymen som ska återställas finns på enheten, så vi inte rehydrate (eftersom data är redan tillgänglig lokalt). 

* Som standard när du återställer startas återuppväckning jobb som proaktivt rehydrate data baserat på den termiska kartan. 

Windows PowerShell-cmdlets kan användas att fråga efter jobb som körs återuppväckning, avbryts återuppväckning och hämta status för jobbet återuppväckning i uppdatering 4.

* `Get-HcsRehydrationJob` – Denna cmdlet hämtar status för jobbet återuppväckning. Ett enda återuppväckning jobb utlöses för en volym.

* `Set-HcsRehydrationJob` – Denna cmdlet kan du pausa, stoppa och återuppta jobbet återuppväckning när återuppväckning pågår.

Mer information om återuppväckning cmdletar går du till [cmdlet-referens för Windows PowerShell för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Med automatisk återuppväckning kan vanligtvis förväntas högre tillfälliga läsprestanda. Den faktiska storleken på förbättringar är beroende av olika faktorer, till exempel åtkomstmönster och dataomsättningen datatyp. 

Om du vill avbryta ett jobb för återuppväckning kan du använda PowerShell-cmdleten. Om du vill inaktivera permanent återuppväckning jobb för alla framtida återställningar [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Hur du använder säkerhetskopieringskatalogen

Den **säkerhetskopieringskatalogen** bladet ger ange en fråga som hjälper dig att begränsa din säkerhetskopia. Du kan filtrera de säkerhetskopior som hämtas baserat på följande parametrar:

* **Tidsintervall** – intervallet datum och tid när säkerhetskopian skapades.
* **Enheten** – enheten där säkerhetskopian skapades.
* **Säkerhetskopieringspolicy** eller **volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namn på** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Typ** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymdata som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan väljs ögonblicksbilder av molndata för dataåterhämtning.
* **Storlek** – den verkliga storleken hos säkerhetskopierade.
* **Skapad** – datum och tid när säkerhetskopiorna som har skapats. 
* **Volymer** -antalet volymer som är associerade med säkerhetskopian.
* **Initierade** – säkerhetskopieringar kan initieras automatiskt enligt ett schema eller manuellt av en användare. (Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **säkerhetskopiera** kan ta en interaktiv eller på begäran-säkerhetskopiering.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Hur du återställer din StorSimple-volym från en säkerhetskopia

Du kan använda den **säkerhetskopieringskatalogen** bladet för att återställa din StorSimple-volym från en specifik säkerhetskopia. Kom ihåg men, som återställer en volym återgår volymen till tillståndet den var i när säkerhetskopian skapades. Alla data som lagts till efter säkerhetskopieringen går förlorade.

> [!WARNING]
> Återställa från en säkerhetskopia kommer att ersätta de befintliga volymerna från säkerhetskopian. Detta kan orsaka förlust av data som har skrivits när säkerhetskopian skapades.


### <a name="to-restore-your-volume"></a>Att återställa volymen
1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia på följande sätt:
   
   1. Ange tidsintervallet.
   2. Välj rätt enhet.
   3. I listrutan, väljer du den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja.
   4. Klicka på **tillämpa** att köra frågan.

      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
   
      ![Säkerhetskopian lista](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expandera den säkerhetskopia du vill visa de tillhörande volymerna. Dessa volymer måste vara offline på värden och enheten innan du kan återställa dem. Åtkomst till volymerna på den **volymer** bladet för din enhet, och följ sedan stegen i [koppla från en volym](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) att ta dem offline.
   
   > [!IMPORTANT]
   > Kontrollera att du har vidtagit volymer från värden först innan du utför volymerna som är offline på enheten. Om du inte vidtar volymerna som är offline på värden, kan det leda till att data skadas.
   
4. Gå tillbaka till den **säkerhetskopieringskatalogen** fliken och välj en säkerhetskopia. Högerklicka och välj sedan snabbmenyn **återställa**.

    ![Säkerhetskopian lista](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Du uppmanas att bekräfta. Granska informationen om återställning och markera sedan kryssrutan bekräftelse.
   
    ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klicka på **återställa**. Detta startar ett jobb för återställning som du kan visa genom att öppna den **jobb** sidan.

   ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. När återställningen är klar kan du kontrollera att innehållet i dina volymer har ersatts av volymer från säkerhetskopian.


## <a name="if-the-restore-fails"></a>Om återställningen misslyckas

Du får en avisering om återställningen misslyckas av någon anledning. Om detta inträffar kan du uppdatera listan säkerhetskopiering för att verifiera att säkerhetskopian fortfarande är giltig. Om säkerhetskopian är giltig och att du återställer från molnet, kan sedan anslutningsproblem vara orsaken till problemet.

För att slutföra återställningen ta frånkoppla volymen på värden och försök återställa igen. Observera att alla volymdata som utfördes under återställningsprocessen ändringar går förlorade.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

