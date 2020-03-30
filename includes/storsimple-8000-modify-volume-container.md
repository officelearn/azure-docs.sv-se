---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4cffbb1aaa438bac08ec77f576b32a901abacabd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187389"
---
> [!NOTE] 
> Du kan inte ändra krypteringsinställningarna och autentiseringsuppgifterna för lagringskontot som är associerade med en volymbehållare när den har skapats.

#### <a name="to-modify-a-volume-container"></a>Så här ändrar du en volymbehållare

1. Gå till tjänsten StorSimple Device Manager och navigera sedan till **hantering > volymbehållare**.

2. Markera den volymbehållare som du vill ändra i listan över volymbehållare. Markera enheten på sidan **Enheter,** dubbelklicka på den och klicka sedan på fliken **Volymbehållare.**

2. Markera den volymbehållare som du vill ändra i tabelllistan över volymbehållarna. Klicka på **Ändra** från kommandofältet i bladet som öppnas.

    ![Ändra volymbehållare](./media/storsimple-8000-modify-volume-container/modify-vol-container1.png)

3. Gör följande i **behållarbladet Ändra volym:**
   
   1. Det går inte att ändra namn, krypteringsnyckel och lagringskonto som är associerat med volymbehållaren när de har angetts. Ändra den tillhörande bandbreddsinställningen.
      
       ![Ändra bandbreddsinställning](./media/storsimple-8000-modify-volume-container/modify-vol-container2.png)

   2.  Klicka på **OK**.
4. På nästa sida i dialogrutan **Ändra volymbehållare:**
   
   1. Välj en befintlig bandbreddsmall i listrutan.
   2. Granska schemainställningarna för den angivna bandbreddsmallen.
   3. Klicka på **Spara** och bekräfta ändringarna.
      
       ![Bekräfta ändringar](./media/storsimple-8000-modify-volume-container/modify-vol-container3.png)

   3. **Bladet Volymbehållare** uppdateras för att återspegla ändringarna.

