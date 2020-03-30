---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a3ccf76b2722c04a9353fcc7020ff1387bc454c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187546"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Installera en uppdatering från Azure Portal

1. Välj enheten på tjänstesidan StorSimple.

    ![Välj enhet](./media/storsimple-8000-install-update4-via-portal/update1.png)

2. Navigera till **Enhetsinställningar** > **Enhetsuppdateringar**.

    ![Klicka på Enhetsuppdateringar](./media/storsimple-8000-install-update4-via-portal/update2.png)

2. Ett meddelande visas om det finns nya uppdateringar tillgängliga. Du kan också klicka på **Skanna uppdateringar**i bladet **Enhetsuppdateringar** . Det skapas ett jobb för att söka efter tillgängliga uppdateringar. Du meddelas när jobbet har slutförts.

    ![Klicka på Enhetsuppdateringar](./media/storsimple-8000-install-update4-via-portal/update3.png)

3. Vi rekommenderar att du läser den viktiga informationen innan du installerar en uppdatering på enheten. Om du vill installera uppdateringar klickar du på **Installera uppdateringar**. I bladet **Bekräfta regelbundna uppdateringar** läser du förutsättningarna för att slutföra innan du installerar uppdateringar. Markera kryssrutan om du vill visa att du är redo att uppdatera enheten och klicka sedan på **Installera**.

    ![Klicka på Enhetsuppdateringar](./media/storsimple-8000-install-update4-via-portal/update4.png)

6. En uppsättning nödvändiga kontrollerar startar. Dessa kontroller omfattar följande:
   
   * **Hälsokontroller av styrenheten** för att verifiera att båda styrenheterna är felfria och online.
   * **Hälsokontroller för maskinvarukomponenter** för att verifiera att alla maskinvarukomponenter på StorSimple-enheten är felfria.
   * **DATA 0-kontroller** för att verifiera att DATA 0 är aktiverat på enheten. Om inte det här gränssnittet är aktiverat måste du aktivera det och sedan försöka igen.

     Uppdateringen hämtas och installeras endast om alla kontroller har slutförts. Du får ett meddelande när kontrollerna pågår. Om prechecks misslyckas, då du kommer att förses med orsakerna till misslyckande. Åtgärda dessa problem och försök sedan igen. Du kan behöva kontakta Microsofts support om du inte kan lösa dessa problem själv.

7. När förkontrollerna har slutförts skapas ett uppdateringsjobb. Du får ett meddelande när uppdateringsjobbet har skapats.
   
    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update6.png)
   
    Uppdateringen tillämpas sedan på enheten.

9. Uppdateringen tar några timmar att slutföra. Markera uppdateringsjobbet och klicka på **Information** så kan du visa information om jobbet när som helst.

    ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update8.png)

     Du kan också övervaka hur uppdateringsjobbet fortskrider från **Enhetsinställningar > jobb**. På bladet **Jobb** kan du se uppdateringsframsteget.

     ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update7.png)

10. När jobbet är klart navigerar du till **enhetsinställningarna > enhetsuppdateringar**. Programvaruversionen bör nu uppdateras.

   ![Uppdatera skapande av jobb](./media/storsimple-8000-install-update4-via-portal/update9.png)

