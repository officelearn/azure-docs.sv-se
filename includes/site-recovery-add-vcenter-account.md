---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: cd18d71d26410767a2d3119c12a1339bdc84bd33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107142"
---
1. Starta CSPSConfigtool.exe på konfigurationsservern. Filen finns som en genväg på skrivbordet och även på följande sökväg: *installationsplatsen*\home\svsystems\bin.
2. Klicka på **Hantera konton** > **Lägg till konto**.

    ![Lägg till konto](./media/site-recovery-add-vcenter-account/credentials1.png)
3. I **Kontoinformation** lägger du till kontot som ska användas för automatisk identifiering.

    ![Information](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
   > Det kan ta 15 minuter eller mer innan kontonamnet visas i portalen. Om du vill uppdatera omedelbart klickar du på **Konfigurationsservrar** > ***servernamn*** > **Uppdatera server**.
