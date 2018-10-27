---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 360fd9247684fea483d744d9be77d095b667d66d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165657"
---
<!--author=alkohli last changed: 07/05/2017-->

> [!NOTE] 
> Du kan inte ändra krypteringsinställningarna och autentiseringsuppgifterna för lagringskontot som är associerade med en volymbehållare när den har skapats.

#### <a name="to-modify-a-volume-container"></a>Ändra en volymbehållare

1. Gå till StorSimple Device Manager-tjänsten och gå sedan till **Management > volymbehållare**.

2. Tabellista över volymbehållare, Välj den volymbehållare som du vill ändra. På den **enheter** sidan, väljer enheten, dubbelklicka på den och klicka sedan på den **volymbehållare** fliken.

2. I listan med volymbehållarna, väljer du den volymbehållare som du vill ändra. I bladet som öppnas klickar du på **ändra** i kommandofältet.

    ![Ändra volymbehållare](./media/storsimple-8000-modify-volume-container/modify-vol-container1.png)

3. I den **ändra volymbehållare** bladet gör följande:
   
   1. Namn, krypteringsnyckeln och storage-konto som är associerade med volymcontainern kan inte ändras när de har angetts. Ändra inställningen för associerade bandbredd.
      
       ![Ändra inställningen för bandbredd](./media/storsimple-8000-modify-volume-container/modify-vol-container2.png)

   2.  Klicka på **OK**.
4. På nästa sida av den **ändra Volymbehållare** dialogrutan:
   
   1. Välj en befintlig bandbreddsmall för från den nedrullningsbara listan.
   2. Granska schemainställningarna för den angivna bandbreddsmallen.
   3. Klicka på **spara** och bekräfta ändringarna.
      
       ![Bekräfta ändringar](./media/storsimple-8000-modify-volume-container/modify-vol-container3.png)

   3. Den **volymbehållare** bladet uppdateras för att återspegla ändringarna.

