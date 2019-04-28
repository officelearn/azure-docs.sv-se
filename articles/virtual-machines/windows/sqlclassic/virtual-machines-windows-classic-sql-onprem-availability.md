---
title: Utöka lokala Always On-Tillgänglighetsgrupper till Azure | Microsoft Docs
description: Den här självstudien används resurser som skapades med den klassiska distributionsmodellen och beskriver hur du använder guiden Lägg till replik i SQL Server Management Studio (SSMS) för att lägga till en Always On Availability Group-replik i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481619"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Utöka lokala Always On-Tillgänglighetsgrupper till Azure
Always On Tillgänglighetsgrupper ger hög tillgänglighet för grupper av databasen genom att lägga till sekundära repliker. De här replikeringarna Tillåt växling över databaser om ett fel uppstår. Dessutom kan de användas att avlasta skrivskyddade arbetsbelastningar eller säkerhetskopieringsåtgärder.

Du kan utöka den lokala Tillgänglighetsgrupper till Microsoft Azure genom att etablera en eller flera virtuella Azure-datorer med SQL Server och sedan lägga till dem som repliker i din lokala Tillgänglighetsgrupper.

Den här självstudien förutsätter att du har följande:

* En aktiv Azure-prenumeration. Du kan [registrera dig för en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En befintlig Always On Availability Group på plats. Mer information om Tillgänglighetsgrupper i finns i [ständigt aktiverade Tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx).
* Anslutningen mellan det lokala nätverket och Azure-nätverk. Mer information om hur du skapar det här virtuella nätverket finns i [skapa en plats-till-plats-anslutning med hjälp av Azure portal (klassisk)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="add-azure-replica-wizard"></a>Azure-replik guiden Lägg till
Det här avsnittet visar hur du använder den **guiden för Lägg till Azure-replik** att utöka din lösning för Always On Availability Group om du vill inkludera Azure repliker.

> [!IMPORTANT]
> Den **guiden för Lägg till Azure-replik** stöder bara virtuella datorer som skapats med den klassiska distributionsmodellen. Nya VM-distributioner bör använda den nya Resource Manager-modellen. Om du använder virtuella datorer med Resource Manager måste du manuellt lägga till den sekundära Azure repliken med hjälp av Transact-SQL-commmands (visas inte här). Den här guiden fungerar inte i Resource Manager-scenario.

1. I SQL Server Management Studio expanderar **alltid på hög tillgänglighet** > **Tillgänglighetsgrupper** > **[namn i Tillgänglighetsgruppen]**.
2. Högerklicka på **Tillgänglighetsrepliker**, klicka sedan på **lägga till repliken**.
3. Som standard den **lägga till repliken för att Availability Group Wizard** visas. Klicka på **Nästa**.  Om du har valt den **Visa inte den här sidan igen** alternativet längst ned på sidan under en föregående Start i den här guiden, den här skärmen visas inte.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Du kommer att behöva ansluta till alla befintliga sekundära repliker. Du kan klicka på **Connect...** bredvid varje replik eller du kan klicka på **ansluta alla...** längst ned på skärmen. När autentisering, klickar du på **nästa** att gå vidare till nästa skärm.
5. På den **ange repliker** kan flera flikar visas längst upp: **Repliker**, **slutpunkter**, **säkerhetskopiera inställningar**, och **lyssnare**. Från den **repliker** fliken **lägga till Azure replikering...** att starta guiden Lägg till Azure-replikering.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Välj en befintlig Azure-Hanteringscertifikatet från det lokala certifikatarkivet för Windows om du har installerat en innan. Välj eller ange id för en Azure-prenumeration om du har använt en innan. Du kan klicka på ladda ned för att hämta och installera en Azure-Hanteringscertifikatet hämta listan över prenumerationer med hjälp av ett Azure-konto.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Du ska fylla i fälten på sidan med värden som används för att skapa den Azure virtuell dator (VM) som är värd för repliken.
   
   | Inställning | Beskrivning |
   | --- | --- |
   | **Avbildning** |Välj önskad kombination av OS- och SQL Server |
   | **VM-storlek** |Välj storlek för virtuell dator som bäst passar dina affärsbehov |
   | **Namn på virtuell dator** |Ange ett unikt namn för den nya virtuella datorn. Namnet måste innehålla mellan 3 och 15 tecken, kan innehålla endast bokstäver, siffror och bindestreck, och måste börja med en bokstav och sluta med en bokstav eller siffra. |
   | **VM-användarnamn** |Ange ett användarnamn som ska bli administratörskontot på den virtuella datorn |
   | **VM-administratörslösenord** |Ange ett lösenord för det nya kontot |
   | **Bekräfta lösenord** |Bekräfta lösenordet för det nya kontot |
   | **Virtual Network** |Ange Azure-nätverket som den nya virtuella datorn ska använda. Mer information om virtuella nätverk finns i [översikt över Virtual Network](../../../virtual-network/virtual-networks-overview.md). |
   | **Undernät för virtuellt nätverk** |Ange det virtuella undernätet som den nya virtuella datorn ska använda |
   | **Domän** |Bekräfta förifyllda värdet för domänen är korrekt |
   | **Domänanvändarnamn** |Ange ett konto som tillhör gruppen lokala administratörer på de lokala noderna |
   | **Lösenord** |Ange lösenordet för domänanvändarnamn |
8. Klicka på **OK** att verifiera distributionsinställningarna.
9. Juridiska villkor visas bredvid. Läs och klicka på **OK** om du samtycker till villkoren.
10. Den **ange repliker** visas igen. Kontrollera inställningarna för den nya Azure repliken på den **repliker**, **slutpunkter**, och **säkerhetskopiering inställningar** flikar. Ändra inställningarna för att uppfylla dina affärsbehov.  Mer information om de parametrar som finns på de här flikarna, se [ange repliker sida (guiden Ny tillgänglighetsgrupp guiden/Lägg till replik)](https://msdn.microsoft.com/library/hh213088.aspx). Observera att lyssnare inte kan skapas med hjälp av fliken lyssnare för Tillgänglighetsgrupper som innehåller Azure repliker. Dessutom, om en lyssnare har redan skapats innan du startar guiden, visas ett meddelande om att det inte stöds i Azure. Vi ska titta på hur du skapar en lyssnare inom den **skapa en Tillgänglighetsgruppslyssnare** avsnittet.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klicka på **Nästa**.
12. Väljer du vilken metod för synkronisering av data som du vill använda på den **Välj inledande datasynkronisering** och klicka på **nästa**. För de flesta fall väljer **Fullständig datasynkronisering**. Mer information om data synkroniseringsmetoder finns i [Välj inledande synkronisering datasida (alltid på tillgänglighet grupp guider)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Granska resultaten på den **verifiering** sidan. Korrigera utestående problem och kör verifieringen om det behövs. Klicka på **Nästa**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Granska inställningarna på den **sammanfattning** sidan och klicka sedan på **Slutför**.
15. Etableringen börjar. När guiden är klar klickar du på **Stäng** att avsluta guiden.

> [!NOTE]
> Guiden Lägg till Azure-replikering skapar en loggfil i Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Den här loggfilen kan användas för att felsöka misslyckade Azure-replik-distributioner. Om guiden inte köra alla åtgärder, återställs alla tidigare åtgärder, inklusive ta bort den etablerade virtuella datorn.
> 
> 

## <a name="create-an-availability-group-listener"></a>Skapa en tillgänglighetsgruppslyssnare
När du har skapat tillgänglighetsgruppen bör du skapa en lyssnare för klienter att ansluta till replikerna. Tillgänglighetsgruppslyssnarnas direkt inkommande anslutningar till primärt eller en skrivskyddad sekundär replik. Läs mer på lyssnare [konfigurera en ILB-lyssnare för Always On-Tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Nästa steg
Förutom att använda den **guiden för Lägg till Azure-replik** för att utöka din ständigt aktiverad tillgänglighetsgrupp till Azure, du kan också flytta vissa SQL Server-arbetsbelastningar helt till Azure. Kom igång genom att se [etablera en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Andra ämnen som rör som kör SQL Server i virtuella Azure-datorer, se [SQL Server på Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

