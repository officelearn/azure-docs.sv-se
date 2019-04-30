---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108448"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Bestämma DNS-namnet på en virtuell dator
För att kunna ansluta till SQL Server-databasmotorn från en annan dator måste du känna till DNS-namnet (Domain Name System) för den virtuella datorn. (Detta är det namn som används för att identifiera den virtuella datorn på Internet. Du kan använda IP-adressen, men IP-adress kan ändras när Azure flyttar resurser för redundans eller underhåll. DNS-namnet är stabilt eftersom det kan omdirigeras till en ny IP-adress.)  

1. Välj **Virtuella datorer (klassiska)** i Azure Portal (eller från föregående steg).
2. Välj den virtuella SQL-datorn.
3. Kopiera **DNS-namnet** för den virtuella datorn på bladet **Virtuell dator**.
   
    ![DNS-namn](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Anslut till databasmotorn från en annan dator
1. Öppna SQL Server Management Studio på en dator som är ansluten till Internet.
2. I rutan **Servernamn** i dialogrutan **Anslut till server** eller **Connect to Database Engine** (Anslut till databasmotor) anger du DNS-namnet på den virtuella datorn (som bestämdes i föregående uppgift) och ett portnummer för offentlig slutpunkt i formatet *DNS-namn,portnummer*, till exempel **mysqlvm.cloudapp.net,57500**.
   
    ![Ansluta med SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Om du inte kommer ihåg portnumret för offentlig slutpunkt, som du skapade tidigare, hittar du det i området **Slutpunkter** på bladet **Virtuell dator**.
   
    ![Offentlig port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. I rutan **Autentisering**, markerar du **SQL Server-autentisering**.
4. I rutan **Inloggning** skriver du namnet på en inloggning som du har skapat i en tidigare uppgift.
5. I rutan **Lösenord** skriver du lösenordet för den inloggning som du skapade i en tidigare uppgift.
6. Klicka på **Anslut**.

