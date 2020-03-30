---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187294"
---
1. Expandera **Roller i Klusterhanteraren**för redundans och markera sedan din tillgänglighetsgrupp.  

2. Högerklicka på lyssnarens namn på fliken **Resurser** och klicka sedan på **Egenskaper**.

3. Klicka på fliken **Beroenden.** Om flera resurser visas kontrollerar du att IP-adresserna har ELLER, inte OCH, beroenden.  

4. Klicka på **OK**.

5. Högerklicka på lyssnarens namn och klicka sedan på **Ta med online**.

6. När lyssnaren är online högerklickar du på tillgänglighetsgruppen på fliken **Resurser** och klickar sedan på **Egenskaper**.
   
    ![Konfigurera resursen för tillgänglighetsgruppen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Skapa ett beroende av lyssnarens namnresurs (inte namnet på IP-adressresurser) och klicka sedan på **OK**.
   
    ![Lägga till beroende av lyssnarens namn](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Starta SQL Server Management Studio och anslut sedan till den primära repliken.

9. Gå till **AlwaysOn-tillgänglighetsgrupper** > **Availability Groups** > **\<\>** med hög tillgänglighet > **Gruppnamnsavlyssnare.**  
    Lyssnarnamnet som du skapade i Redundansklusterhanteraren ska visas.

10. Högerklicka på lyssnarens namn och klicka sedan på **Egenskaper**.

11. I rutan **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av den $EndpointPort som du använde tidigare (i den här självstudien var 1433 standard) och klicka sedan på **OK**.

