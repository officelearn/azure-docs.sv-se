---
title: "Hantera katalogen för säkerhetskopieringar StorSimple | Microsoft Docs"
description: "Beskriver hur du använda sidan StorSimple Enhetshanteraren säkerhetskopieringskatalogen listan, Välj och ta bort säkerhetskopior."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att hantera katalogen för säkerhetskopieringar
## <a name="overview"></a>Översikt
Tjänsten StorSimple Enhetshanteraren **säkerhetskopieringskatalogen** bladet visar alla säkerhetskopior som skapas vid manuell eller schemalagda säkerhetskopieringar. Du kan använda den här sidan för att visa alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller använda en säkerhetskopia att återställa eller klona en volym.

Den här självstudiekursen beskrivs hur du väljer, och ta bort en säkerhetskopia. Mer information om hur du återställer din enhet från säkerhetskopia, gå till [återställa enheten från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md). Information om hur du klona en volym, gå till [klona en StorSimple-volym](storsimple-8000-clone-volume-u2.md).

![Säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Den **säkerhetskopieringskatalogen** bladet innehåller en fråga för att begränsa säkerhetskopian uppsättning val. Du kan filtrera de säkerhetskopior som hämtas, baserat på följande parametrar:

* **Enheten** – enheten där säkerhetskopian skapades.
* **Princip för säkerhetskopiering eller volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.
* **Från och till** – intervallet datum och tid när säkerhetskopian skapades.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namnet** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Storlek** – den verkliga storleken för säkerhetskopian.
* **Skapad den** – datum och tid då säkerhetskopieringarna skapades. 
* **Typen** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymens data som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan molnögonblicksbilder väljs för dataåterhämtning.
* **Initierades av** – säkerhetskopieringar kan initieras automatiskt av ett schema eller manuellt av en användare. Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **ta säkerhetskopia** kan ta en manuell säkerhetskopiering.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista över säkerhetskopior för en princip för säkerhetskopiering
Utför följande steg om du vill visa alla säkerhetskopior för en princip för säkerhetskopiering.

#### <a name="to-list-backup-sets"></a>Att lista säkerhetskopior
1. Gå till Enhetshanteraren din StorSimple-tjänsten och klicka på **säkerhetskopieringskatalog**.

2. Filtrera valen på följande sätt:
   
   1. Ange tidsintervall.
   2. Välj lämplig enhet.
   3. Filtrera efter **säkerhetskopiera princip** att visa motsvarande säkerhetskopieringar.
   3. I listrutan säkerhetskopieringsprincip, Välj **alla** visa alla säkerhetskopior på den valda enheten.
   4. Klicka på **tillämpa** att köra frågan.
      
      De säkerhetskopior som är associerade med den valda säkerhetskopieringsprincipen ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Välj en säkerhetskopia
Utför följande steg för att välja en säkerhetskopia för en volym eller en princip för säkerhetskopiering.

#### <a name="to-select-a-backup-set"></a>Att välja en säkerhetskopia
1. Gå till Enhetshanteraren din StorSimple-tjänsten och klicka på **säkerhetskopieringskatalog**.
2. Filtrera valen på följande sätt:
   
   1. Ange tidsintervall. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym eller säkerhetskopiering princip för säkerhetskopiering som du vill välja.
   4. Klicka på **tillämpa** att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Markera och utöka en säkerhetskopia. Du kan nu se de säkerhetskopior som är fördelade på de volymer som den innehåller. Den **återställa** och **ta bort** alternativ är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Du kan utföra någon av dessa åtgärder på den säkerhetskopia som du har valt.

    ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Ta bort en säkerhetskopia
Ta bort en säkerhetskopiering när du inte längre vill behålla data som är associerade med den. Utför följande steg för att ta bort en säkerhetskopia.

#### <a name="to-delete-a-backup-set"></a>Ta bort en säkerhetskopia
 Gå till Enhetshanteraren din StorSimple-tjänsten och klicka på **säkerhetskopieringskatalog**.
2. Filtrera valen på följande sätt:
   
   1. Ange tidsintervall. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym eller säkerhetskopiering princip för säkerhetskopiering som du vill välja.
   4. Klicka på **tillämpa** att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Markera och utöka en säkerhetskopia. Du kan nu se de säkerhetskopior som är fördelade på de volymer som den innehåller. Den **återställa** och **ta bort** alternativ är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Högerklicka på den valda säkerhetskopian och på snabbmenyn Välj **ta bort**.

    ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. När du uppmanas att bekräfta, granska informationen som visas och klicka på **ta bort**. Den valda säkerhetskopian tas bort permanent.

    ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Du meddelas när borttagningen är pågående och när den har slutförts. Uppdatera frågan på den här sidan när borttagningen är klar. Borttagna säkerhetskopian visas inte längre i listan över säkerhetskopior.

    ![Gå till katalogen för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder Säkerhetskopiering katalogen för att återställa enheten från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

