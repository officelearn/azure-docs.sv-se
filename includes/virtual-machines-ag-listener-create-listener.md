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
I det här steget skapar du manuellt tillgänglighetsgruppavlyssnaren i Redundansklusterhanteraren och SQL Server Management Studio.

1. Öppna Redundansklusterhanteraren från noden som är värd för den primära repliken.

2. Markera noden **Nätverk** och notera sedan klusternätverksnamnet. Det här namnet används i variabeln $ClusterNetworkName i PowerShell-skriptet.

3. Expandera klusternamnet och klicka sedan på **Roller**.

4. Högerklicka på namnet på tillgänglighetsgruppen i fönstret **Roller** och välj sedan Lägg till åtkomstpunkt för**resursklient** **Add Resource** > .
   
    ![Lägga till klientåtkomstpunkt för tillgänglighetsgrupp](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Skapa ett namn för den nya lyssnaren i rutan **Namn,** klicka på **Nästa** två gånger och klicka sedan på **Slutför**.  
    Ta inte lyssnaren eller resursen online just nu.

6. Klicka på fliken **Resurser** och expandera sedan klientåtkomstpunkten som du just skapade. 
    IP-adressresursen för varje klusternätverk i klustret visas. Om detta är en Azure-lösning visas bara en IP-adressresurs.

7. Gör något av följande:
   
   * Så här konfigurerar du en hybridlösning:
     
        a. Högerklicka på den IP-adressresurs som motsvarar ditt lokala undernät och välj sedan **Egenskaper**. Observera IP-adressnamnet och nätverksnamnet.
   
        b. Välj **Statisk IP-adress,** tilldela en oanvänd IP-adress och klicka sedan på **OK**.
 
   * Så här konfigurerar du en Azure-lösning:

        a. Högerklicka på IP-adressresursen som motsvarar ditt Azure-undernät och välj sedan **Egenskaper**.
       
       > [!NOTE]
       > Om lyssnaren senare inte är online på grund av en IP-adress i konflikt som valts av DHCP, kan du konfigurera en giltig statisk IP-adress i det här egenskapsfönstret.
       > 
       > 

       b. Ändra **IP-adressnamn**i samma fönstret EGENSKAPER för **IP-adress** .  
        Det här namnet används i den $IPResourceName variabeln för PowerShell-skriptet. Om din lösning sträcker sig över flera virtuella Azure-nätverk upprepar du det här steget för varje IP-resurs.

