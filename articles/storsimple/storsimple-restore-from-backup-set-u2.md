---
title: "Återställa en StorSimple-volym från en säkerhetskopia | Microsoft Docs"
description: "Förklarar hur du använder säkerhetskopieringskatalogen tjänstsidan StorSimple Manager för att återställa en StorSimple-volym från en säkerhetskopia."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6f289c39-96c7-4d57-b68a-4bc2e99aef9d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 76fa3dd8fa2f9775dc166686e699a8dd66399aff
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set-update-2"></a>Återställa en StorSimple-volym från en säkerhetskopia (uppdatering 2)
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [återställer en StorSimple-volym från en säkerhetskopia (uppdatering 2)](storsimple-8000-restore-from-backup-set-u2.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Översikt
Den **säkerhetskopieringskatalogen** sidan visas alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Använd den här sidan om du vill visa och hantera säkerhetskopior, återställa från en säkerhetskopia eller klona en volym.

 ![Säkerhetskopiera katalog](./media/storsimple-restore-from-backup-set-u2/restore.png)

Den här självstudiekursen beskrivs hur du använder den **säkerhetskopieringskatalogen** att återställa enheten från en säkerhetskopia.

Du kan återställa en volym från en lokal eller säkerhetskopiering i molnet. I båda fallen ger återställningen volymen online direkt medan data hämtas i bakgrunden. 

## <a name="before-you-restore"></a>Innan du återställer
Innan du startar en återställning, bör du vara medveten om följande varningar:

* **Kopplar från volymen** – ta volymen offline på både värden och enheten innan du startar återställningen. Även om återställningen öppnar automatiskt volymen online på enheten, måste du manuellt ta enheten online på värden. Du kan aktivera volymen online på värden när volymen är online på enheten. (Du behöver inte vänta tills återställningen har slutförts.) Mer information om procedurer går du till [kopplar från en volym](storsimple-manage-volumes-u2.md#take-a-volume-offline).
* **Volymtyp efter återställning** – borttagna volymer återställs baserat på typen i ögonblicksbilden. Volymer som har lokalt Fäst återställs som lokalt fästa volymer och volymer som har nivåer återställs som nivåindelade volymer.
  
    För befintliga volymer åsidosätter aktuella användningstyp volymens den typ som är lagrad i ögonblicksbilden. Till exempel om du återställer en volym från en ögonblicksbild som vidtogs när volymtypen har nivåer och volymtyp nu lokalt Fäst (på grund av en konvertering) återställs sedan volymen som en lokalt Fäst volym. Om en befintlig lokalt Fäst volym expanderas och därefter återställts från en tidigare ögonblicksbild vidtas när volymen var mindre, behåller återställda volymen på samma sätt kan den aktuella utökade storleken.
  
    Du kan inte konvertera en volym från en nivåindelad volym till en lokalt Fäst volym eller _vice versa_ medan volymen återställs. Vänta tills återställningen har slutförts och sedan kan du konvertera volymen till en annan typ. Information om hur du konverterar en volym, gå till [ändra volymtypen av](storsimple-manage-volumes-u2.md#change-the-volume-type). 
* **Volymens storlek avspeglas i den återställda volymen** – detta är viktigt om du återställer en lokalt Fäst volym som har tagits bort (eftersom lokalt fästa volymer är helt etablerad). Kontrollera att du har tillräckligt med utrymme innan du försöker återställa en lokalt Fäst volym som har tagits bort. 
* **Du kan inte utöka en volym när den återställs** – vänta tills återställningen har slutförts innan du försöker att utöka volymen. Information om hur du utökar en volym, gå till [ändra en volym](storsimple-manage-volumes-u2.md#modify-a-volume).
* **Du kan utföra en säkerhetskopiering när du återställer en lokal volym** – procedurer finns på [använda StorSimple Manager-tjänsten för att hantera principer för säkerhetskopiering](storsimple-manage-backup-policies.md).
* **Du kan avbryta en återställningsåtgärd** – om du avbryter återställningsjobbet, och sedan volymen återställs till det tillstånd som innan du påbörjade återställningen. Mer information om procedurer går du till [avbryta ett jobb](storsimple-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Hur återställer arbete
För enheter som kör uppdatering 4 eller senare, implementeras en heatmap-baserad återställning. Som värden begär att få åtkomst till data nå enheten, dessa begäranden spåras och en heatmap skapas. Hög begärandehastighet resulterar i datasegment med högre termiska medan lägre begärandehastighet översätts till segment med lägre värme. Du måste ha åtkomst till data minst två gånger för att markeras som _varm_. En fil som ändras också har markerats som _varm_. När du startar återställningen sker proaktiv hydrering av data baserat på heatmap. För versioner har före uppdatering 4 data hämtats vid återställning baserat på enbart åtkomst. 

Heatmap-baserade spårning aktiveras endast för nivåindelade volymer och lokalt fästa volymer inte stöds. Heatmap-baserad återställning stöds inte även när du klonar en volym till en annan enhet. Om det finns en återställning på plats och en lokal ögonblicksbild för volymen som ska återställas finns på enheten och sedan vi inte rehydrate (eftersom data är redan tillgänglig lokalt). Som standard när du återställer initieras rehydration jobb som proaktivt rehydrate data baserat på heatmap. Windows PowerShell-cmdlets kan användas till fråga rehydration jobb som körs, avbryts rehydration och hämta status för jobbet rehydration i uppdatering 4.

* `Get-HcsRehydrationJob`-Denna cmdlet hämtar rehydration jobbets status. Ett enda rehydration jobb utlöses för en volym.
* `Set-HcsRehydrationJob`-Denna cmdlet kan du pausa, stoppa, återuppta jobbet rehydration när rehydration pågår. 

Mer information om rehydration cmdlets, gå till [Windows PowerShell-cmdlet-referens för StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

Med automatisk rehdyration vanligtvis förväntas högre tillfälligt läsprestanda. Den faktiska magniutde förbättringarna beror på olika faktorer som mönster för dataåtkomst, dataomsättningen och -datatypen. Du kan använda PowerShell-cmdlet om du vill avbryta ett jobb för rehydration. Kontakta Microsoft Support om du vill inaktivera permanent rehydration jobb för alla framtida återställningar.

## <a name="how-to-use-the-backup-catalog"></a>Hur du använder den säkerhetskopiera katalogen
Den **säkerhetskopieringskatalogen** innehåller en fråga som hjälper dig att begränsa säkerhetskopian ange. Du kan filtrera de säkerhetskopior som hämtas baserat på följande parametrar:

* **Enheten** – enheten där säkerhetskopian skapades.
* **Säkerhetskopiera princip** eller **volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.
* **Från** och **till** – intervallet datum och tid när säkerhetskopian skapades.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namnet** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Storlek** – den verkliga storleken för säkerhetskopian.
* **Skapas på** – datum och tid då säkerhetskopieringarna skapades. 
* **Typen** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten. En ögonblicksbild i molnet refererar till säkerhetskopian av volymens data som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan molnögonblicksbilder väljs för dataåterhämtning.
* **Initierad av** – säkerhetskopieringar kan initieras automatiskt enligt ett schema eller manuellt av dig. (Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **ta säkerhetskopia** kan ta en interaktiv säkerhetskopiering.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Hur du återställer din StorSimple-volym från en säkerhetskopia
Du kan använda den **säkerhetskopieringskatalogen** att återställa din StorSimple-volym från en specifik säkerhetskopia. Men kom ihåg att återställa en volym återgår volymen till tillståndet den var i när säkerhetskopian skapades. Alla data som lagts till efter säkerhetskopieringen går förlorade.

> [!WARNING]
> Återställa från en säkerhetskopia ersätter de befintliga volymerna från säkerhetskopian. Detta kan orsaka förlust av data som har skrivits när säkerhetskopian skapades.
> 
> 

### <a name="to-restore-your-volume"></a>Att återställa din volym
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken.
   
    ![Säkerhetskopieringskatalogen](./media/storsimple-restore-from-backup-set-u2/restore.png)
2. Välj en säkerhetskopia på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på kryssikonen ![kryssikon](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png) att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
3. Expandera den säkerhetskopia du vill visa associerade volymer. Dessa volymer måste vara offline på värden och enheten innan du kan återställa dem. Åtkomst till volymerna på den **Volymbehållare** , och följ stegen i [kopplar från en volym](storsimple-manage-volumes-u2.md#take-a-volume-offline) ta offline.
   
   > [!IMPORTANT]
   > Kontrollera att du har vidtagit volymer offline på värden först innan du utför volymerna som är offline på enheten. Om du inte vidtar volymerna som är offline på värden, leda det potentiellt till att data skadas.
   > 
   > 
4. Gå tillbaka till den **säkerhetskopieringskatalogen** fliken och markera en säkerhetskopia.
5. Klicka på **återställa** längst ned på sidan.
6. Du uppmanas att bekräfta. Granska informationen om återställning och markera sedan kryssrutan bekräftelse.
   
    ![Bekräftelsesida](./media/storsimple-restore-from-backup-set-u2/ConfirmRestore.png)
7. Klicka på kryssikonen ![kryssikon](./media/storsimple-restore-from-backup-set-u2/HCS_CheckIcon.png). En återställningsjobbet börjar. Du kan visa jobbet genom att öppna den **jobb** sidan. 
8. När återställningen är klar kan kontrollera du att innehållet i volymerna som ersätts av volymer från säkerhetskopian.

![Video tillgänglig](./media/storsimple-restore-from-backup-set-u2/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du kan använda klonen och återställa funktioner i StorSimple för att återställa borttagna filer, klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="if-the-restore-fails"></a>Om återställningen misslyckas
Du får en avisering om återställningen misslyckas av någon anledning. Om detta inträffar kan du uppdatera listan säkerhetskopiering för att kontrollera att säkerhetskopian är fortfarande giltig. Om säkerhetskopian är giltig och att du återställer från molnet, kan sedan anslutningsproblem vara orsaken till problemet. 

För att slutföra återställningen åtgärda volymen offline på värden och försök utföra återställningen. Alla ändringar som utfördes under återställningsprocessen volymens data går förlorade.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera StorSimple-volymer](storsimple-manage-volumes-u2.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

