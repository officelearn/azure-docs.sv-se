---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818835"
---
#### <a name="to-create-a-manual-backup"></a>Så här skapar du en manuell säkerhetskopia

1. Gå till StorSimple Device Manager-tjänsten och klicka sedan på **Enheter**. Välj din enhet i listan med enheter. Gå till **Inställningar > Hantera > Principer för säkerhetskopiering**.

2. Bladet **Principer för säkerhetskopiering** innehåller alla principer för säkerhetskopiering i tabellformat, inklusive principen för den volym som du vill säkerhetskopiera. Välj principen som är kopplad till den volym som du vill säkerhetskopiera och högerklicka för att öppna snabbmenyn. Välj **Säkerhetskopiera nu** i listrutan.

    ![Skapa en manuell säkerhetskopia](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. Utför följande steg på bladet **Säkerhetskopiera nu**:

    1. Välj lämplig **typ av ögonblicksbild** i listrutan: **Lokala** ögonblicksbild eller **molnet** ögonblicksbild. Välj lokal ögonblicksbild för snabbare säkerhetskopieringar eller återställningar, och ögonblicksbild av molndata för dataåterhämtning.

        ![Skapa en manuell säkerhetskopia](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. Starta ett jobb för att skapa en ögonblicksbild genom att klicka på **OK**. Ett meddelande visas längst upp på sidan när jobbet har skapats.

        ![Skapa en manuell säkerhetskopia](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. Klicka på meddelandet för att övervaka jobbet. När du gör det öppnas bladet **Jobb** där du kan visa jobbförloppet.


5. När säkerhetskopieringsjobbet är slutfört, går du till fliken **Säkerhetskopieringskatalog**.

6. Ställ in filterval till lämplig enhet, säkerhetskopieringsprincip och tidsintervall. Säkerhetskopian borde visas i listan över säkerhetskopieringsuppsättningar som visas i katalogen.

