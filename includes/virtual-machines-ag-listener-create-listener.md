---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67187292"
---
I det här steget skapar du tillgänglighets gruppens lyssnare manuellt i Klusterhanteraren för växling vid fel och SQL Server Management Studio.

1. Öppna Klusterhanteraren för växling vid fel från noden som är värd för den primära repliken.

2. Välj noden **nätverk** och anteckna sedan klustrets nätverks namn. Det här namnet används i $ClusterNetworkName-variabeln i PowerShell-skriptet.

3. Expandera kluster namnet och klicka sedan på **roller**.

4. I fönstret **roller** högerklickar du på namnet på tillgänglighets gruppen och väljer sedan **Lägg till resurs** > **klient åtkomst punkt**.
   
    ![Lägg till klient åtkomst punkt för tillgänglighets grupp](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. I rutan **namn** skapar du ett namn för den nya lyssnaren, klickar på **Nästa** två gånger och sedan på **Slutför**.  
    Ta inte med lyssnaren eller resursen online just nu.

6. Klicka på fliken **resurser** och expandera sedan den klient åtkomst punkt som du nyss skapade. 
    IP-adressresursen för varje kluster nätverk i klustret visas. Om det här är en Azure-Only-lösning visas bara en IP-adressresurs.

7. Gör något av följande:
   
   * Konfigurera en hybrid lösning:
     
        a. Högerklicka på IP-adressresursen som motsvarar ditt lokala undernät och välj sedan **Egenskaper**. Anteckna IP-adressens namn och nätverks namnet.
   
        b. Välj **statisk IP-adress**, tilldela en oanvänd IP-adress och klicka sedan på **OK**.
 
   * Konfigurera en Azure-Only-lösning:

        a. Högerklicka på IP-adressresursen som motsvarar ditt Azure-undernät och välj sedan **Egenskaper**.
       
       > [!NOTE]
       > Om lyssnaren senare inte kan anslutas på grund av en IP-adress i konflikt som valts av DHCP, kan du konfigurera en giltig statisk IP-adress i fönstret Egenskaper.
       > 
       > 

       b. Ändra **IP-adressen**i fönstret Egenskaper för **IP-adress** .  
        Det här namnet används i $IPResourceName-variabeln för PowerShell-skriptet. Om din lösning omfattar flera virtuella Azure-nätverk upprepar du det här steget för varje IP-resurs.

