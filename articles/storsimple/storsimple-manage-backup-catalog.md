---
title: "Hantera katalogen för säkerhetskopieringar StorSimple | Microsoft Docs"
description: "Beskriver hur du använda sidan StorSimple Manager säkerhetskopieringskatalogen listan, Välj och ta bort säkerhetskopior för en volym."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 732ae04a8ae5f85ed154370c680d87af2ba5ee39
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>Använda StorSimple Manager-tjänsten för att hantera katalogen för säkerhetskopieringar
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Om du vill visa versionen av den här artikeln för den nya Azure portalen, gå till [använda StorSimple Manager-tjänsten för att hantera katalogen för säkerhetskopieringar](storsimple-8000-manage-backup-catalog.md). Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
StorSimple Manager-tjänsten **säkerhetskopieringskatalogen** sidan visas alla säkerhetskopior som skapas vid manuell eller schemalagda säkerhetskopieringar. Du kan använda den här sidan för att visa alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller använda en säkerhetskopia att återställa eller klona en volym.

Den här självstudiekursen beskrivs hur du väljer, och ta bort en säkerhetskopia. Mer information om hur du återställer din enhet från säkerhetskopia, gå till [återställa enheten från en säkerhetskopia](storsimple-restore-from-backup-set.md). Information om hur du klona en volym, gå till [klona en StorSimple-volym](storsimple-clone-volume.md).

![Säkerhetskopieringskatalogen](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

Den **säkerhetskopieringskatalogen** innehåller en fråga för att begränsa säkerhetskopian ange. Du kan filtrera de säkerhetskopior som hämtas, baserat på följande parametrar:

* **Enheten** – enheten där säkerhetskopian skapades.
* **Säkerhetskopiera princip- eller** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.
* **Från och till** – intervallet datum och tid när säkerhetskopian skapades.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namnet** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Storlek** – den verkliga storleken för säkerhetskopian.
* **Skapad den** – datum och tid då säkerhetskopieringarna skapades. 
* **Typen** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymens data som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan molnögonblicksbilder väljs för dataåterhämtning.
* **Initierades av** – säkerhetskopieringar kan initieras automatiskt av ett schema eller manuellt av en användare. Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **ta säkerhetskopia** kan ta en manuell säkerhetskopiering.

## <a name="list-backup-sets-for-a-volume"></a>Lista över säkerhetskopior för en volym
Utför följande steg om du vill visa alla säkerhetskopior för en volym.

#### <a name="to-list-backup-sets"></a>Att lista säkerhetskopior
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken.
2. Filtrera valen på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj en volym och visa motsvarande säkerhetskopieringar i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) att köra frågan.
      
      De säkerhetskopior som är associerade med den valda volymen ska visas i listan över säkerhetskopieringsuppsättningar.

## <a name="select-a-backup-set"></a>Välj en säkerhetskopia
Utför följande steg för att välja en säkerhetskopia för en volym eller en princip för säkerhetskopiering.

#### <a name="to-select-a-backup-set"></a>Att välja en säkerhetskopia
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog** fliken.
2. Filtrera valen på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
3. Markera och utöka en säkerhetskopia. Den **återställa** och **ta bort** alternativ visas längst ned på sidan. Du kan utföra någon av dessa åtgärder på den säkerhetskopia som du har valt.

## <a name="delete-a-backup-set"></a>Ta bort en säkerhetskopia
Ta bort en säkerhetskopiering när du inte längre vill behålla data som är associerade med den. Utför följande steg för att ta bort en säkerhetskopia.

#### <a name="to-delete-a-backup-set"></a>Ta bort en säkerhetskopia
1. På tjänstsidan StorSimple Manager-på **säkerhetskopieringskatalog fliken**.
2. Filtrera valen på följande sätt:
   
   1. Välj lämplig enhet.
   2. Välj den volym eller säkerhetskopiering principen för säkerhetskopiering som du vill välja i den nedrullningsbara listan.
   3. Ange tidsintervall.
   4. Klicka på kryssikonen ![Kryssikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.
3. Markera och utöka en säkerhetskopia. Den **återställa** och **ta bort** alternativ visas längst ned på sidan. Klicka på **Ta bort**.
4. Du meddelas när borttagningen är pågående och när den har slutförts. Uppdatera frågan på den här sidan när borttagningen är klar. Borttagna säkerhetskopian visas inte längre i listan över säkerhetskopior.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder Säkerhetskopiering katalogen för att återställa enheten från en säkerhetskopia](storsimple-restore-from-backup-set.md).
* Lär dig hur du [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

