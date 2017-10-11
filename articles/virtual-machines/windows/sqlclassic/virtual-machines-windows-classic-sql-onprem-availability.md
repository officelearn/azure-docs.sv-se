---
title: "Utöka lokala Always On-Tillgänglighetsgrupper till Azure | Microsoft Docs"
description: "Den här kursen använder resurser som har skapats med den klassiska distributionsmodellen och beskriver hur du använder guiden Lägg till replik i SQL Server Management Studio (SSMS) för att lägga till en replik av en Always On-Tillgänglighetsgruppen i Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 50326a093adaf3558c56dfd0b38544f0e60be460
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Utöka lokala Always On-Tillgänglighetsgrupper till Azure
Always On-Tillgänglighetsgrupper ger hög tillgänglighet för grupper av databasen genom att lägga till sekundära repliker. De här replikeringarna tillåter inte att redundansväxla databaser om ett fel uppstår. Dessutom kan de användas att avlasta skrivskyddade arbetsbelastningar eller aktiviteter för säkerhetskopiering.

Du kan utöka lokala Tillgänglighetsgrupper till Microsoft Azure genom att etablera en eller flera virtuella Azure-datorer med SQL Server och sedan lägga till dem som repliker i din lokala Tillgänglighetsgrupper.

Den här kursen förutsätter att du har följande:

* En aktiv Azure-prenumeration. Du kan [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En befintlig alltid på tillgänglighetsgrupp lokalt. Mer information om Tillgänglighetsgrupper i finns [alltid på Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx).
* Anslutningen mellan lokala nätverk och dina virtuella Azure-nätverket. Mer information om hur du skapar det här virtuella nätverket finns [skapa en plats-till-plats-anslutning med hjälp av Azure portal (klassisk)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="add-azure-replica-wizard"></a>Azure replik guiden Lägg till
Det här avsnittet visar hur du använder den **guiden för Lägg till Azure-replik** att utöka din lösning för Always On-Tillgänglighetsgruppen för att inkludera Azure repliker.

> [!IMPORTANT]
> Den **guiden för Lägg till Azure-replik** stöder endast virtuella datorer som skapats med den klassiska distributionsmodellen. Den nya VM distributioner bör använda nyare Resource Manager-modellen. Om du använder virtuella datorer med Resource Manager måste du manuellt lägga till den sekundära Azure repliken med hjälp av Transact-SQL-commmands (visas inte här). Den här guiden fungerar inte i Resource Manager-scenariot.

1. Från inom SQL Server Management Studio, expandera **alltid på hög tillgänglighet** > **Tillgänglighetsgrupper** > **[namn på Tillgänglighetsgruppen]**.
2. Högerklicka på **Tillgänglighetsrepliker**, klicka på **lägga till replik**.
3. Som standard den **lägga till repliken med Availability Group Wizard** visas. Klicka på **Nästa**.  Om du har valt den **Visa inte den här sidan igen** alternativet längst ned på sidan under föregående lanseringen av den här guiden kan den här skärmen visas inte.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Du kommer att behöva ansluta till alla befintliga sekundära repliker. Du kan klicka på **Anslut...** bredvid varje replik eller du kan klicka på **ansluta alla...** längst ned på skärmen. När autentisering, klickar du på **nästa** att gå vidare till nästa sida.
5. På den **ange repliker** sidan flera flikar visas överst: **repliker**, **slutpunkter**, **säkerhetskopiering inställningar**, och  **Lyssnare**. Från den **repliker** klickar du på **lägga till Azure-replik...** Starta guiden Lägg till Azure replik.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Välj en befintlig Azure-Hanteringscertifikatet från det lokala certifikatarkivet Windows om du har installerat en innan. Välj eller ange id för en Azure-prenumeration om du har använt en innan. Du kan klicka på Hämta om du vill hämta och installera Azure-Hanteringscertifikatet och hämta listan över prenumerationer med hjälp av ett Azure-konto.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Du kan fylla i fälten på sidan med värden som används för att skapa Azure virtuell dator (VM) som är värd för repliken.
   
   | Inställning | Beskrivning |
   | --- | --- |
   | **Bild** |Välj önskad kombination av OS- och SQL Server |
   | **VM-storlek** |Välj storleken på VM som bäst passar dina affärsbehov |
   | **Namn på virtuell dator** |Ange ett unikt namn för den nya virtuella datorn. Namnet måste innehålla mellan 3 och 15 tecken, kan innehålla endast bokstäver, siffror och bindestreck, och måste börja med en bokstav och sluta med en bokstav eller siffra. |
   | **VM-användarnamn** |Ange ett användarnamn som ska bli administratörskontot på den virtuella datorn |
   | **Administratörslösenord för VM** |Ange ett lösenord för det nya kontot |
   | **Bekräfta lösenord** |Bekräfta lösenordet för det nya kontot |
   | **Virtual Network** |Ange virtuella Azure-nätverket som den nya virtuella datorn ska använda. Mer information om virtuella nätverk finns [översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md). |
   | **Undernät för virtuellt nätverk** |Ange det virtuella nätverket som den nya virtuella datorn ska använda |
   | **Domän** |Bekräfta att värdet i förväg för domänen är korrekta |
   | **Namnet på användaren** |Ange ett konto som tillhör gruppen lokala administratörer på de lokala klusternoderna |
   | **Lösenord** |Ange lösenordet för användarnamnet för domänen |
8. Klicka på **OK** att verifiera distributionsinställningarna för.
9. Juridiska villkoren visas bredvid. Läs och klicka på **OK** om du samtycker till villkoren.
10. Den **ange repliker** visas igen. Kontrollera inställningarna för den nya Azure repliken på den **repliker**, **slutpunkter**, och **säkerhetskopiering inställningar** flikar. Ändra inställningarna för att uppfylla dina affärsbehov.  Mer information om de parametrar som finns på de här flikarna, se [ange repliker sida (guiden Ny tillgänglighetsgrupp guiden/Add replik)](https://msdn.microsoft.com/library/hh213088.aspx). Observera att lyssnare inte kan skapas med fliken Listener för Tillgänglighetsgrupper som innehåller Azure repliker. Dessutom, om en lyssnare har redan skapats innan du startar guiden, visas ett meddelande som anger att den inte stöds i Azure. Vi ska titta på hur du skapar lyssnare i den **skapa en Tillgänglighetsgruppslyssnare** avsnitt.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klicka på **Nästa**.
12. Välj synkroniseringsmetoden data du vill använda på den **Välj inledande datasynkronisering** och klickar på **nästa**. För de flesta fall väljer **Fullständig datasynkronisering**. Mer information om metoder för synkronisering av data finns [inledande Data synkronisering på sidan Välj (alltid på tillgänglighet grupp guider)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Granska resultaten på den **validering** sidan. Korrigera utestående problem och kör verifieringen igen om det behövs. Klicka på **Nästa**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Granska inställningarna på den **sammanfattning** sidan och klicka sedan på **Slutför**.
15. Etableringen börjar. När guiden är klar klickar du på **Stäng** att avsluta guiden.

> [!NOTE]
> Guiden Lägg till Azure repliken skapar en loggfil i Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Den här loggfilen kan användas för att felsöka misslyckade Azure replik distributioner. Om guiden inte några åtgärden kördes, återställs alla tidigare åtgärder, inklusive ta bort den etablerade virtuella datorn.
> 
> 

## <a name="create-an-availability-group-listener"></a>Skapa en tillgänglighetsgruppslyssnare
När tillgänglighetsgruppen har skapats, bör du skapa en lyssnare för klienter att ansluta till replikerna. Lyssnare direkt inkommande anslutningar till den primära servern eller en skrivskyddad sekundär replik. Mer information om lyssnare finns [konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Nästa steg
Förutom att använda den **guiden för Lägg till Azure-replik** utöka alltid på Tillgänglighetsgruppen till Azure, du kan också flytta vissa SQL Server-arbetsbelastningar helt till Azure. Om du vill komma igång finns [etablering av en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Andra avsnitt relaterade till SQL Server som körs i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

