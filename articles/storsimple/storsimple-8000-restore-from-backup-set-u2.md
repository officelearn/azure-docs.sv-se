---
title: Återställa en volym från en säkerhetskopia på en StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du använder tjänsten StorSimple Enhetshanteraren säkerhetskopieringskatalogen för att återställa en StorSimple-volym från en säkerhetskopia.
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
ms.openlocfilehash: aff0710ead4f76bb80c38e2d88fe9cd3ce6a7b48
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875461"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Återställa en StorSimple-volym från en säkerhetskopia

## <a name="overview"></a>Översikt

Den här självstudiekursen beskriver återställningen utförs på en StorSimple 8000 series-enhet med en befintlig säkerhetskopia. Använd den **säkerhetskopieringskatalog** bladet för att återställa en volym från en lokal eller säkerhetskopiering i molnet. Den **säkerhetskopieringskatalog** bladet visar alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Återställningen från en säkerhetskopia ger volymen online direkt medan data hämtas i bakgrunden.

En alternativ metod för att starta återställningen är att gå till **enheter > [enheten] > volymer**. I den **volymer** bladet Välj en volym, högerklicka om du vill anropa snabbmenyn och välj sedan **återställa**.

## <a name="before-you-restore"></a>Innan du återställer

Innan du startar en återställning kan du granska följande varningar:

* **Du måste koppla från volymen** – ta volymen offline på både värden och enheten innan du startar återställningen. Även om återställningen öppnar automatiskt volymen online på enheten, måste du manuellt ta enheten online på värden. Du kan aktivera volymen online på värden som volymen är online på enheten. (Du behöver inte vänta tills återställningen har slutförts.) Mer information om procedurer går du till [kopplar från en volym](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volymtyp efter återställning** – borttagna volymer återställs baserat på typen i ögonblicksbilden; som är lokalt fasta volymer återställs som lokalt fästa volymer och volymer som har nivåer återställs som nivåindelade volymer.

    För befintliga volymer åsidosätter aktuella användningstyp volymens den typ som är lagrad i ögonblicksbilden. Till exempel om du återställer en volym från en ögonblicksbild som vidtogs när volymtypen har nivåer och volymtyp nu lokalt Fäst (på grund av en konvertering som utfördes) kommer sedan volymen att återställas som en lokalt Fäst volym. Om en befintlig lokalt Fäst volym har utökats och därefter återställts från en tidigare ögonblicksbild vidtas när volymen var mindre, behåller återställda volymen på samma sätt, den aktuella utökade storleken.

    Du kan inte konvertera en volym från en nivåindelad volym till en lokalt Fäst volym eller en lokalt Fäst volym till en nivåindelad volym när volymen återställs. Vänta tills återställningen har slutförts och sedan kan du konvertera volymen till en annan typ. Information om hur du konverterar en volym, gå till [ändra volymtypen av](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Volymens storlek avspeglas i den återställda volymen** – detta är viktigt om du återställer en lokalt Fäst volym som har tagits bort (eftersom lokalt fästa volymer är helt etablerad). Kontrollera att du har tillräckligt med utrymme innan du försöker återställa en lokalt Fäst volym som har tagits bort.

* **Du kan inte utöka en volym när den återställs** – vänta tills återställningen har slutförts innan du försöker att utöka volymen. Information om hur du utökar en volym, gå till [ändra en volym](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Du kan utföra en säkerhetskopiering när du återställer en lokal volym** – procedurer finns på [använda Enhetshanteraren för StorSimple-tjänsten för att hantera principer för säkerhetskopiering](storsimple-8000-manage-backup-policies-u2.md).

* **Du kan avbryta en återställningsåtgärd** – om du avbryter återställningsjobbet, och sedan på volymen kommer att återställas till tillståndet innan du påbörjade återställningen. Mer information om procedurer går du till [avbryta ett jobb](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hur återställer arbete

För enheter som kör uppdatering 4 eller senare, implementeras en heatmap-baserad återställning. Som värden begär att få åtkomst till data nå enheten, dessa begäranden spåras och en heatmap skapas. Hög begärandehastighet resulterar i datasegment med högre termiska medan lägre begärandehastighet översätts till segment med lägre värme. Du måste ha åtkomst till data minst två gånger för att markeras som _varm_. En fil som ändras också har markerats som _varm_. När du startar återställningen sker proaktiv hydrering av data baserat på heatmap. För versioner ned före uppdatering 4 data under återställning baserat på enbart åtkomst.

Följande villkor gäller heatmap-baserad återställning:

* Heatmap spårning aktiveras endast för nivåindelade volymer och lokalt fästa volymer stöds inte.

* Heatmap-baserad återställning stöds inte när du klonar en volym till en annan enhet. 

* Om det finns en återställning på plats och en lokal ögonblicksbild för volymen som ska återställas finns på enheten och sedan vi inte rehydrate (eftersom data är redan tillgänglig lokalt). 

* Som standard när du återställer initieras rehydration jobb som proaktivt rehydrate data baserat på heatmap. 

Windows PowerShell-cmdlets kan användas till fråga rehydration jobb som körs, avbryts rehydration och hämta status för jobbet rehydration i uppdatering 4.

* `Get-HcsRehydrationJob`-Denna cmdlet hämtar rehydration jobbets status. Ett enda rehydration jobb utlöses för en volym.

* `Set-HcsRehydrationJob`-Denna cmdlet kan du pausa, stoppa, återuppta jobbet rehydration när rehydration pågår.

Mer information om rehydration cmdlets, gå till [Windows PowerShell-cmdlet-referens för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Med automatisk rehdyration vanligtvis förväntas högre tillfälligt läsprestanda. Den faktiska magniutde förbättringarna beror på olika faktorer som mönster för dataåtkomst, dataomsättningen och -datatypen. 

Du kan använda PowerShell-cmdlet om du vill avbryta ett jobb för rehydration. Om du vill inaktivera permanent rehydration jobb för alla framtida återställningar [kontaktar Microsoft Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Hur du använder den säkerhetskopiera katalogen

Den **säkerhetskopieringskatalogen** bladet innehåller en fråga som hjälper dig att begränsa säkerhetskopian uppsättning val. Du kan filtrera de säkerhetskopior som hämtas baserat på följande parametrar:

* **Tidsintervallet** – intervallet datum och tid när säkerhetskopian skapades.
* **Enheten** – enheten där säkerhetskopian skapades.
* **Säkerhetskopiera princip** eller **volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namnet** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Typen** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymens data som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan molnögonblicksbilder väljs för dataåterhämtning.
* **Storlek** – den verkliga storleken för säkerhetskopian.
* **Skapas på** – datum och tid då säkerhetskopieringarna skapades. 
* **Volymer** -antalet volymer som är associerade med säkerhetskopian.
* **Initierade** – säkerhetskopieringar kan initieras automatiskt enligt ett schema eller manuellt av en användare. (Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **ta säkerhetskopia** kan ta en interaktiv eller på begäran-säkerhetskopia.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Hur du återställer din StorSimple-volym från en säkerhetskopia

Du kan använda den **säkerhetskopieringskatalogen** bladet för att återställa din StorSimple-volym från en specifik säkerhetskopia. Men kom ihåg att återställa en volym återställs volymen till tillståndet den var i när säkerhetskopian skapades. Alla data som lagts till efter säkerhetskopieringen går förlorade.

> [!WARNING]
> Återställa från en säkerhetskopia ersätter de befintliga volymerna från säkerhetskopian. Detta kan orsaka förlust av data som har skrivits när säkerhetskopian skapades.


### <a name="to-restore-your-volume"></a>Att återställa din volym
1. Gå till Enhetshanteraren för StorSimple-tjänsten och klicka sedan på **säkerhetskopieringskatalog**.

2. Välj en säkerhetskopia på följande sätt:
   
   1. Ange tidsintervall.
   2. Välj lämplig enhet.
   3. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   4. Klicka på **tillämpa** att köra frågan.

    Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
   
    ![Säkerhetskopian lista](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Expandera den säkerhetskopia du vill visa associerade volymer. Dessa volymer måste vara offline på värden och enheten innan du kan återställa dem. Åtkomst till volymerna på den **volymer** bladet för din enhet och följ stegen i [kopplar från en volym](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) ta offline.
   
   > [!IMPORTANT]
   > Kontrollera att du har vidtagit volymer offline på värden först innan du utför volymerna som är offline på enheten. Om du inte vidtar volymerna som är offline på värden, leda det potentiellt till att data skadas.
   
4. Gå tillbaka till den **säkerhetskopieringskatalogen** fliken och markera en säkerhetskopia. Högerklicka och klicka sedan på snabbmenyn **återställa**.

    ![Säkerhetskopian lista](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Du uppmanas att bekräfta. Granska informationen om återställning och markera sedan kryssrutan bekräftelse.
   
    ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7.  Klicka på **återställa**. Detta startar ett jobb för återställning som du kan visa genom att öppna den **jobb** sidan.

    ![Bekräftelsesida](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. När återställningen är klar kan du kontrollera att innehållet i volymerna som ersätts av volymer från säkerhetskopian.


## <a name="if-the-restore-fails"></a>Om återställningen misslyckas

Du får en avisering om återställningen misslyckas av någon anledning. Om detta inträffar kan du uppdatera listan säkerhetskopiering för att kontrollera att säkerhetskopian är fortfarande giltig. Om säkerhetskopian är giltig och att du återställer från molnet, kan sedan anslutningsproblem vara orsaken till problemet.

För att slutföra återställningen åtgärda volymen offline på värden och försök utföra återställningen. Observera att alla ändringar som utfördes under återställningsprocessen volymens data går förlorade.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera StorSimple-volymer](storsimple-8000-manage-volumes-u2.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

