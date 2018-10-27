---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: abef93d30ff9cdf1ecdbbfd450d9f96e6352a513
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165817"
---
<!--author=alkohli last changed: 06/22/17-->

#### <a name="to-create-a-volume-container"></a>Skapa en volymcontainer
1. Gå till StorSimple Device Manager-tjänsten och klicka på **Enheter**. Markera och klicka på en enhet i tabellistan med enheter. 

    ![Blad för volymcontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. Klicka på **+ Lägg till volymcontainer** på instrumentpanelen för enheten

    ![Blad för volymcontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. På bladet **Lägg till volymcontainer**:
   
   1. Enheten markeras automatiskt.
   2. Ange ett **namn** för din volymcontainer. Namnet måste vara mellan 3 och 32 tecken. Du kan byta namn på en volymcontainer när den har skapats.
   3. Välj **Aktivera kryptering av molnlagring** för att aktivera kryptering av data som skickas från enheten till molnet.
   4. Ange och bekräfta en **krypteringsnyckel för molnlagring** som är 8 till 32 tecken. Nyckeln används av enheten för att få åtkomst till krypterad data.
   5. Ange ett **lagringskonto** att associera med volymcontainern. Du kan välja ett befintligt lagringskonto eller standardkontot som genereras när tjänsten skapas. Du kan också använda alternativet **Lägg till ny** för att ange ett lagringskonto som inte är länkad till den här tjänstprenumerationen.
   6. Välj **Obegränsad** i listrutan **Ange bandbredd** om du vill använda all tillgänglig bandbredd. Du kan också välja alternativet **Anpassat** om du vill använda bandbreddskontroller, och sedan ange ett värde mellan 1 Mbit/s och 1 000 Mbit/s.
      Om du har informationen om din bandbreddsanvändning tillgänglig, kan du allokera bandbredd enligt ett schema genom att ange **Välj en bandbreddsmall**. Stegvisa instruktioner för proceduren finns i [Lägg till en bandbreddsmall](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template).

      ![Blad för volymcontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. Klicka på **Skapa**.

        ![Blad för volymcontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       Du får ett meddelande när volymcontainern har skapats.

       ![Meddelande om att volymcontainern har skapats](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   Den nya volymcontainern visas i listan med volymcontainrar för enheten.

   ![Bladet Lägg till volymcontainer](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


