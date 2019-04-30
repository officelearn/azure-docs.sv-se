---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097733"
---
I det här steget skapar du manuellt tillgänglighetsgruppens lyssnare i hanteraren för redundanskluster och SQL Server Management Studio.

1. Öppna Hanteraren för redundanskluster från den nod som är värd för den primära repliken.

2. Välj den **nätverk** noden och Observera klustrets nätverksnamn. Det här namnet används i variabeln $ClusterNetworkName i PowerShell-skript.

3. Expandera klusternamnet och klicka sedan på **roller**.

4. I den **roller** fönstret högerklickar du på tillgänglighetsgruppens namn och välj sedan **Lägg till resurs** > **klientåtkomstpunkt**.

    ![Lägg till klientåtkomstpunkten för tillgänglighetsgruppen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. I den **namn** , skapar du ett namn för den här nya lyssnaren, klickar du på **nästa** två gånger, och klicka sedan på **Slutför**.  
    Tar inte med lyssnaren eller resursen i det här läget.

6. Klicka på den **resurser** fliken och expandera sedan den klientåtkomstpunkt som du nyss skapade. 
    IP-adressresurs för varje klusternätverk i klustret visas. Om det här är en Azure-lösningen, visas endast en IP-adressresurs.

7. Gör något av följande:

   * Konfigurera en lösning för hybridmoln:

        a. Högerklicka på IP-adressresursen som motsvarar det lokala undernätet och välj sedan **egenskaper**. Notera IP-adressnamn och nätverksnamn.

        b. Välj **statisk IP-adress**, tilldela en oanvänd IP-adress och klicka sedan på **OK**.

   * Konfigurera en Azure-lösningen:

        a. Högerklicka på IP-adressresursen som motsvarar din Azure-undernätet och välj sedan **egenskaper**.

       > [!NOTE]
       > Om lyssnaren inte senare är online på grund av en konflikt IP-adress som väljs ut av DHCP kan konfigurera du en statisk IP-adress i det här egenskapsfönstret.
       > 
       > 

       b. I samma **IP-adress** i fönstret Ändra den **IP-adressnamn**.  
        Det här namnet används i variabeln $IPResourceName på PowerShell-skript. Om din lösning sträcker sig över flera Azure-nätverk, kan du upprepa det här steget för varje IP-resurs.
        
<!-- Update_Description: update meta properties -->