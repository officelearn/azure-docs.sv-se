---
title: Hantera din Säkerhetskatalog för StorSimple | Microsoft-dokument
description: I artikeln beskrivs hur du ankar katalogsidan för säkerhetskopiering av Tjänsten StorSimple Device Manager för att lista, välja och ta bort säkerhetskopior.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319691"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>Använda Tjänsten StorSimple Device Manager för att hantera din säkerhetskopieringskatalog
## <a name="overview"></a>Översikt
Bladet StorSimple Device **Manager-tjänst Backup Catalog** visar alla säkerhetskopieringsuppsättningar som skapas när manuella eller schemalagda säkerhetskopior tas. Du kan använda den här sidan för att visa alla säkerhetskopior för en säkerhetskopieringsprincip eller en volym, markera eller ta bort säkerhetskopior eller använda en säkerhetskopia för att återställa eller klona en volym.

I den här självstudien beskrivs hur du listar, markerar och tar bort en säkerhetskopia. Om du vill veta hur du återställer enheten från säkerhetskopiering går du till [Återställ enheten från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md). Om du vill veta hur du klonar en volym går du till [Klona en StorSimple-volym](storsimple-8000-clone-volume-u2.md).

![Katalog för säkerhetskopiering](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

**Bladet Säkerhetskopieringskatalog** innehåller en fråga för att begränsa markeringen för säkerhetskopian. Du kan filtrera de säkerhetskopieringsuppsättningar som hämtas, baserat på följande parametrar:

* **Enhet** – Enheten där säkerhetskopian skapades.
* **Säkerhetskopieringsprincip eller Volym** – Säkerhetskopieringsprincipen eller volymen som är associerad med den här säkerhetskopian.
* **Från och till** – Datum- och tidsintervallet när säkerhetskopian skapades.

De filtrerade säkerhetskopieringsuppsättningarna tabuleras sedan baserat på följande attribut:

* **Namn** – Namnet på säkerhetskopieringsprincipen eller volymen som är associerad med säkerhetskopian.
* **Storlek** – Den faktiska storleken på säkerhetskopian.
* **Skapad på** – Datum och tid då säkerhetskopieringarna skapades. 
* **Typ** – Säkerhetskopieringsuppsättningar kan vara lokala ögonblicksbilder eller ögonblicksbilder av molnet. En lokal ögonblicksbild är en säkerhetskopia av alla volymdata som lagras lokalt på enheten, medan en ögonblicksbild i molnet refererar till säkerhetskopiering av volymdata som finns i molnet. Lokala ögonblicksbilder ger snabbare åtkomst, medan ögonblicksbilder i molnet väljs för dataåtersåterkoppling.
* **Initieras av** – Säkerhetskopiorna kan initieras automatiskt genom ett schema eller manuellt av en användare. Du kan använda en princip för säkerhetskopiering för att schemalägga säkerhetskopior. Du kan också använda alternativet **Ta säkerhetskopiera** för att göra en manuell säkerhetskopiering.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista säkerhetskopior för en säkerhetskopieringsprincip
Slutför följande steg för att lista alla säkerhetskopior för en säkerhetskopieringsprincip.

#### <a name="to-list-backup-sets"></a>Så här listar du säkerhetskopior
1. Gå till tjänsten StorSimple Device Manager och klicka på **Säkerhetskopieringskatalog**.

2. Filtrera markeringarna enligt följande:
   
   1. Ange tidsintervallet.
   2. Välj lämplig enhet.
   3. Filtrera **efter säkerhetskopieringsprincip** om du vill visa motsvarande säkerhetskopior.
   3. I listrutan säkerhetskopieringsprincip väljer du **Alla** om du vill visa alla säkerhetskopior på den valda enheten.
   4. Klicka på **Använd** för att köra den här frågan.
      
      De säkerhetskopior som är associerade med den valda säkerhetskopieringsprincipen ska visas i listan över säkerhetskopior.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Välj en säkerhetskopia
Slutför följande steg för att välja en säkerhetskopia för en volym- eller säkerhetskopieringsprincip.

#### <a name="to-select-a-backup-set"></a>Så här väljer du en säkerhetskopia
1. Gå till tjänsten StorSimple Device Manager och klicka på **Säkerhetskopieringskatalog**.
2. Filtrera markeringarna enligt följande:
   
   1. Ange tidsintervallet. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym- eller säkerhetskopieringsprincip för den säkerhetskopia som du vill välja.
   4. Klicka på **Använd** för att köra den här frågan.
      
      De säkerhetskopior som är associerade med den valda volym- eller säkerhetskopieringsprincipen ska visas i listan över säkerhetskopior.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Markera och expandera en säkerhetskopia. Du kan nu se säkerhetskopieringsuppsättningarna uppdelade efter de volymer som den innehåller. Alternativen **Återställ** och **ta bort** är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Du kan utföra någon av dessa åtgärder på säkerhetskopian som du har valt.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>Ta bort en säkerhetskopia
Ta bort en säkerhetskopia när du inte längre vill behålla de data som är associerade med den. Gör följande för att ta bort en säkerhetskopia.

#### <a name="to-delete-a-backup-set"></a>Så här tar du bort en säkerhetskopia
 Gå till tjänsten StorSimple Device Manager och klicka på **Säkerhetskopieringskatalog**.
1. Filtrera markeringarna enligt följande:
   
   1. Ange tidsintervallet. 
   2. Välj lämplig enhet. 
   3. Filtrera efter volym- eller säkerhetskopieringsprincip för den säkerhetskopia som du vill välja.
   4. Klicka på **Använd** för att köra den här frågan.
      
      De säkerhetskopior som är associerade med den valda volym- eller säkerhetskopieringsprincipen ska visas i listan över säkerhetskopior.

      ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Markera och expandera en säkerhetskopia. Du kan nu se säkerhetskopieringsuppsättningarna uppdelade efter de volymer som den innehåller. Alternativen **Återställ** och **ta bort** är tillgängliga via snabbmenyn (högerklicka) för säkerhetskopian. Högerklicka på den markerade säkerhetskopian och välj **Ta bort**på snabbmenyn .

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. När du uppmanas att bekräfta, granska den visade informationen och klicka på **Ta bort**. Den valda säkerhetskopian tas bort permanent.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Du får ett meddelande när borttagningen pågår och när den har slutförts. När borttagningen är klar uppdaterar du frågan på den här sidan. Den borttagna säkerhetskopian visas inte längre i listan över säkerhetskopior.

    ![Gå till säkerhetskopieringskatalogen](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder säkerhetskopieringskatalogen för att återställa enheten från en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md).
* Lär dig hur du [använder Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

