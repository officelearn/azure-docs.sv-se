---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 290bca5f3839356817651096e6a274c868a5a268
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221926"
---
1. Starta CSPSConfigtool.exe på konfigurationsservern. Filen finns som en genväg på skrivbordet och även på följande sökväg: *installationsplatsen*\home\svsystems\bin.
2. Klicka på **Hantera konton** > **Lägg till konto**.

    ![Lägg till konto](./media/site-recovery-add-vcenter-account/credentials1.png)
3. I **Kontoinformation** lägger du till kontot som ska användas för automatisk identifiering.

    ![Information](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
  > Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart klickar du på **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.
