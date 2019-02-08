---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889286"
---
### <a name="to-take-a-backup"></a>Gör en säkerhetskopia

1. Gå till StorSimple Device Manager-tjänsten. Markera och klicka på din enhet i tabellistan med enheter och klicka sedan på **Alla inställningar**. Gå till **Inställningar > Hantera > Säkerhetskopieringspolicy** på bladet **Inställningar**.

    ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Klicka på **+ Lägg till princip** på bladet **Säkerhetskopieringspolicy**.

    ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu2.png)

3. På bladet **Skapa säkerhetskopieringspolicy** anger du ett namn som innehåller mellan 3 och 150 tecken för säkerhetskopieringspolicyn.

4. Välj de volymer som ska säkerhetskopieras. Om du väljer mer än en volym grupperas volymerna för att skapa en kraschkonsekvent säkerhetskopia.

    ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu4.png)

5. På bladet **Lägg till första schema**:

    1. Välj typen av säkerhetskopiering. För snabbare återställningar väljer du **Lokal ögonblicksbild**. För dataåterhämtning väljer du ögonblicksbilden av **molndata**.
    2. Ange frekvensen för säkerhetskopiering i minuter, timmar, dagar eller veckor.
    3. Välj en kvarhållningstid. Kvarhållningsalternativen beror på frekvensen för säkerhetskopiering. För en daglig princip, kan kvarhållning anges i veckor, medan kvarhållningen för en månatlig princip är i månader.
    4. Välj starttiden och datum för principen för säkerhetskopiering.
    5. Skapa säkerhetskopieringspolicyn genom att klicka på **OK**.

        ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Starta genereringen av säkerhetskopieringspolicyn genom att klicka på **Skapa**. Du får ett meddelande när säkerhetskopieringspolicyn har skapats. Listan över säkerhetskopieringspolicyer uppdateras också.
      
      ![Lägg till princip för säkerhetskopiering](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Nu har du en säkerhetskopieringspolicy som skapar schemalagda säkerhetskopieringar av din volymdata.




