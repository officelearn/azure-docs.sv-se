---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "67187294"
---
1. Expandera **roller**i Klusterhanteraren för växling vid fel och markera sedan din tillgänglighets grupp.  

2. På fliken **resurser** högerklickar du på namnet på lyssnaren och klickar sedan på **Egenskaper**.

3. Klicka på fliken **beroenden** . Om flera resurser visas kontrollerar du att IP-adresserna har eller, inte och, är beroende av varandra.  

4. Klicka på **OK**.

5. Högerklicka på namnet på lyssnaren och klicka sedan på **ta online**.

6. När lyssnaren är online går du till fliken **resurser** , högerklickar på tillgänglighets gruppen och klickar sedan på **Egenskaper**.
   
    ![Konfigurera tillgänglighets grupps resursen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Skapa ett beroende på resursen för lyssnar namn (inte namnet på IP-adressresursen) och klicka sedan på **OK**.
   
    ![Lägg till beroende på lyssnarens namn](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Starta SQL Server Management Studio och Anslut sedan till den primära repliken.

9. Gå till **AlwaysOn High Availability**tillgänglighets  >  **Availability Groups**  >  **\<AvailabilityGroupName\>**  >  **grupps lyssnare**för tillgänglighets grupper med hög tillgänglighet för AlwaysOn.  
    Det lyssnar namn som du skapade i Klusterhanteraren för växling vid fel ska visas.

10. Högerklicka på namnet på lyssnaren och klicka sedan på **Egenskaper**.

11. I rutan **port** anger du Port numret för tillgänglighets gruppens lyssnare genom att använda $EndpointPort som du använde tidigare (i den här självstudien är 1433 standard) och klickar sedan på **OK**.

