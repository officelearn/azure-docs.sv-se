---
title: Hantera din StorSimple-säkerhetskopieringskatalogen | Microsoft Docs
description: Beskriver hur du använder sidan StorSimple Device Manager säkerhetskopieringskatalogen listan, Välj och ta bort säkerhetskopior.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076793"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Använda StorSimple Device Manager-tjänsten för att hantera din säkerhetskopieringskatalogen
## <a name="overview"></a>Översikt
StorSimple Device Manager-tjänsten **säkerhetskopieringskatalogen** bladet visar alla säkerhetskopior som skapas när manuell eller schemalagd säkerhetskopiering utförs. Du kan använda den här sidan för att lista alla säkerhetskopior för en princip för säkerhetskopiering eller en volym, Välj eller ta bort säkerhetskopior eller Använd en säkerhetskopia att återställa eller klona en volym.

Den här självstudien beskrivs hur du väljer, och ta bort en säkerhetskopia. Om du vill veta hur du återställer din enhet från en säkerhetskopia kan du gå till [återställa din enhet från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md). Om du vill lära dig mer om att klona en volym, gå till [klona en StorSimple-volym](storsimple-8000-clone-volume-u2.md).

![Säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

Den **säkerhetskopieringskatalogen** bladet ger ange en fråga för att begränsa din säkerhetskopia. Du kan filtrera de säkerhetskopior som hämtas, baserat på följande parametrar:

* **Enheten** – enheten där säkerhetskopian skapades.
* **Princip för säkerhetskopiering eller volym** – den princip för säkerhetskopiering eller en volym som är associerade med den här säkerhetskopian.
* **Från och till** – intervallet datum och tid när säkerhetskopian skapades.

Filtrerade säkerhetskopiorna visas sedan som en tabell baserat på följande attribut:

* **Namn på** – namnet på den princip för säkerhetskopiering eller en volym som är associerade med säkerhetskopian.
* **Storlek** – den verkliga storleken hos säkerhetskopierade.
* **Skapad den** – datum och tid när säkerhetskopiorna som har skapats. 
* **Typ** – säkerhetskopior kan lokala ögonblicksbilder eller molnbaserade ögonblicksbilder. En lokal ögonblicksbild är en säkerhetskopia av din volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet som refererar till säkerhetskopian av volymdata som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst medan väljs ögonblicksbilder av molndata för dataåterhämtning.
* **Initierat av** – säkerhetskopieringar kan initieras automatiskt av ett schema eller manuellt av en användare. Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopieringar. Du kan också använda den **säkerhetskopiera** kan ta en manuell säkerhetskopiering.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista över säkerhetskopior för en princip för säkerhetskopiering
Utför följande steg om du vill visa alla säkerhetskopior för en princip för säkerhetskopiering.

#### <a name="to-list-backup-sets"></a>Att lista säkerhetskopior
1. Gå till StorSimple Device Manager-tjänsten och klicka på **säkerhetskopieringskatalog**.

2. Filtrera valen på följande sätt:
   
   1. Ange tidsintervallet.
   2. Välj rätt enhet.
   3. Filtrera efter **säkerhetskopieringspolicy** att visa motsvarande säkerhetskopiorna.
   3. I listrutan för säkerhetskopieringsprincipen väljer **alla** att visa alla säkerhetskopior på den valda enheten.
   4. Klicka på **tillämpa** att köra frågan.
      
      De säkerhetskopior som är associerade med den valda säkerhetskopieringsprincipen ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Välj en säkerhetskopia
Utför följande steg för att välja en säkerhetskopia för en volym eller en princip för säkerhetskopiering.

#### <a name="to-select-a-backup-set"></a>Att välja en säkerhetskopia
1. Gå till StorSimple Device Manager-tjänsten och klicka på **säkerhetskopieringskatalog**.
2. Filtrera valen på följande sätt:
   
   1. Ange tidsintervallet. 
   2. Välj rätt enhet. 
   3. Filtrera efter volym eller backup-principen för säkerhetskopian som du vill välja.
   4. Klicka på **tillämpa** att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Markera och utöka en säkerhetskopia. Du kan nu se de säkerhetskopior som är fördelade på de volymer som den innehåller. Den **återställa** och **ta bort** alternativ är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Du kan utföra någon av dessa åtgärder på den säkerhetskopia som du har valt.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Ta bort en säkerhetskopia
Ta bort en säkerhetskopia när du inte längre vill behålla data som är associerade med den. Utför följande steg för att ta bort en säkerhetskopia.

#### <a name="to-delete-a-backup-set"></a>Att ta bort en säkerhetskopia
 Gå till StorSimple Device Manager-tjänsten och klicka på **säkerhetskopieringskatalog**.
1. Filtrera valen på följande sätt:
   
   1. Ange tidsintervallet. 
   2. Välj rätt enhet. 
   3. Filtrera efter volym eller backup-principen för säkerhetskopian som du vill välja.
   4. Klicka på **tillämpa** att köra frågan.
      
      Säkerhetskopiorna som är associerade med den valda volymen eller princip för säkerhetskopiering ska visas i listan över säkerhetskopieringsuppsättningar.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Markera och utöka en säkerhetskopia. Du kan nu se de säkerhetskopior som är fördelade på de volymer som den innehåller. Den **återställa** och **ta bort** alternativ är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Högerklicka på den valda säkerhetskopian och på snabbmenyn väljer **ta bort**.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. När du uppmanas att bekräfta granska informationen som visas och klicka på **ta bort**. Den valda säkerhetskopian tas bort permanent.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Du meddelas när borttagningen är pågående och när den har slutförts. När borttagningen är klar kan du uppdatera frågan på den här sidan. Den borttagna säkerhetskopierade visas inte längre i listan över säkerhetskopior.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använda säkerhetskopieringskatalogen för att återställa din enhet från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

