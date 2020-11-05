---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e3bff61cfbf89aee3566d677ccf593b102cff36d
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375885"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>Så här lägger du till en StorSimple-säkerhetskopieringsprincip

1. Gå till din StorSimple-enhet och klicka på **Säkerhetskopieringspolicy**.

2. Klicka på **+ Lägg till princip** i kommandofältet på bladet **Säkerhetskopieringspolicy**.
   
    ![Lägga till en säkerhetskopieringspolicy](./media/storsimple-8000-add-backup-policy-u2/addbupol1.png)

3. Utför följande steg på bladet **Skapa säkerhetskopieringspolicy** :
   
   1. **Välj enhet** fylls i automatiskt baserat på den enhet du valt.
   
   2. Ange ett **namn på** säkerhets kopierings principen som innehåller mellan 3 och 150 tecken. När principen har skapats kan du inte byta namn på den.
       
   3. Du kan tilldela volymer till säkerhetskopieringspolicyn genom att välja **Lägg till volymer** och sedan klicka på kryssrutan eller kryssrutorna i tabellistan över volymer för att koppla en eller flera volymer till säkerhetskopieringspolicyn.

       ![Lägga till en princip för säkerhetskopiering 2](./media/storsimple-8000-add-backup-policy-u2/addbupol2.png)

   4. Du kan definiera ett schema för säkerhetskopieringspolicyn genom att klicka på **Första schemat** och sedan ändra följande parametrar:

       ![Lägg till en princip för säkerhets kopiering 3](./media/storsimple-8000-add-backup-policy-u2/addbupol3.png)

       1. För **Typ av ögonblicksbild** väljer du **Moln** eller **Lokal**.

       2. Ange säkerhets kopierings frekvensen (ange ett tal och välj sedan **dagar** eller **veckor** i list rutan.

       3. Ange ett kvarhållningsschema.

       4. Ange ett datum och en tid då säkerhetskopieringsprincipen ska börja.

       5. Klicka på **OK** för att definiera schemat.

   5. Klicka på **Skapa** för att skapa en säkerhetskopieringspolicy.

       ![Lägg till en säkerhets kopierings princip 4](./media/storsimple-8000-add-backup-policy-u2/addbupol4.png)
   
   6. Du meddelas när säkerhetskopieringspolicyn har skapats. Den nya principen visas i tabellvyn på bladet **Säkerhetskopieringspolicy**.

       ![Lägg till en säkerhets kopierings princip 5](./media/storsimple-8000-add-backup-policy-u2/addbupol7.png)

