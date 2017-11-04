---
title: "Återställa en StorSimple-volym från en säkerhetskopia | Microsoft Docs"
description: "Förklarar hur du använder säkerhetskopieringskatalogen tjänstsidan StorSimple Manager för att återställa en StorSimple-volym från en säkerhetskopia."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: b979782e-3184-4465-ad5f-e4516a5885d2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 12484338f5b4d489604d70a657ef0992b6267297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Återställa en StorSimple-volym från en säkerhetskopia
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## <a name="overview"></a>Översikt
Den **säkerhetskopieringskatalogen** sidan visas alla säkerhetskopior som skapas när manuell eller automatisk säkerhetskopiering utförs. Du kan använda den här sidan för att visa alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller använda en säkerhetskopia att återställa eller klona en volym.

 ![Säkerhetskopiera katalog](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

Den här självstudiekursen beskrivs hur du använder den **säkerhetskopieringskatalogen** sidan om du vill återställa en volym på din enhet från en säkerhetskopia.

## <a name="how-to-use-the-backup-catalog"></a>Hur du använder den säkerhetskopiera katalogen
Den **säkerhetskopieringskatalogen** innehåller en fråga som hjälper dig att begränsa säkerhetskopian ange. Du kan filtrera de säkerhetskopior som hämtas baserat på följande parametrar:

* **Enheten** – enheten där säkerhetskopian skapades.
* **Säkerhetskopiera princip** eller **volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.
* **Från** och **till** – intervallet datum och tid när säkerhetskopian skapades.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namnet** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Storlek** – den verkliga storleken för säkerhetskopian.
* **Skapas på** – datum och tid då säkerhetskopieringarna skapades. 
* **Typen** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymens data som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan molnögonblicksbilder väljs för dataåterhämtning.
* **Initierad av** – säkerhetskopieringar kan initieras automatiskt enligt ett schema eller manuellt av en användare. (Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **ta säkerhetskopia** kan ta en interaktiv säkerhetskopiering.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Hur du återställer din StorSimple-volym från en säkerhetskopia
Du kan använda den **säkerhetskopieringskatalogen** att återställa din StorSimple-volym från en specifik säkerhetskopia. 

> [!WARNING]
> Återställa från en säkerhetskopia ersätter de befintliga volymerna från säkerhetskopian. Detta kan orsaka förlust av data som har skrivits när säkerhetskopian skapades.
> 
> 

Se till att volymen är offline innan du startar en återställning på en volym. Du behöver ta volymen offline på värden först och sedan enheten. Följ stegen i [kopplar från en volym](storsimple-manage-volumes.md#take-a-volume-offline). Utför följande steg om du vill återställa en volym från en säkerhetskopia.

### <a name="to-restore-from-a-backup-set"></a>Att återställa från en säkerhetskopia
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken.
   
    ![Säkerhetskopieringskatalogen](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. Välj en säkerhetskopia på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på kryssikonen ![kryssikon](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png) att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
3. Expandera den säkerhetskopia du vill visa associerade volymer. Dessa volymer måste vara offline på värden och enheten innan du kan återställa dem. Följ stegen i [kopplar från en volym](storsimple-manage-volumes.md#take-a-volume-offline).
   
   > [!IMPORTANT]
   > Kontrollera att du har vidtagit volymer offline på värden först innan du utför volymerna som är offline på enheten. Om du inte vidtar volymerna som är offline på värden, leda det potentiellt till att data skadas.
   > 
   > 
4. Välj en säkerhetskopia. Klicka på **återställa** längst ned på sidan.
5. Du uppmanas att bekräfta. 
   
    ![Bekräftelsesida](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. Granska informationen för återställningen och klicka på kryssikonen ![kryssikon](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png). Detta kommer att initiera en återställningsjobbet som du kan visa genom att öppna den **jobb** sidan. 
7. När återställningen är klar kan kontrollera du att innehållet i volymerna som ersätts av volymer från säkerhetskopian.

![Video tillgänglig](./media/storsimple-restore-from-backup-set/Video_icon.png) **Video tillgänglig**

Om du vill se en video som visar hur du kan använda klonen och återställa funktioner i StorSimple för att återställa borttagna filer, klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/).

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera StorSimple-volymer](storsimple-manage-volumes.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

