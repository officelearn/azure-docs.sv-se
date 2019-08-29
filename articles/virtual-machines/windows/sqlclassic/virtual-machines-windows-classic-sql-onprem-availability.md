---
title: Utöka alla lokala tillgänglighets grupper till Azure | Microsoft Docs
description: Den här självstudien använder resurser som skapats med den klassiska distributions modellen och beskriver hur du använder guiden Lägg till replik i SQL Server Management Studio (SSMS) för att lägga till en Always on Group-replik i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 28819bc9d2eaf7d4b595bed59bcd1df8741b62a5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101835"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Utöka alla lokala tillgänglighets grupper till Azure
Always on-tillgänglighetsgrupper ger hög tillgänglighet för databas grupper genom att lägga till sekundära repliker. Dessa repliker tillåter att databaser Miss lyckas om det skulle uppstå ett problem. Dessutom kan de användas för att avlasta Läs arbets belastningar eller säkerhets kopierings aktiviteter.

Du kan utöka lokala tillgänglighets grupper till Microsoft Azure genom att tillhandahålla en eller flera virtuella Azure-datorer med SQL Server och sedan lägga till dem som repliker i dina lokala tillgänglighets grupper.

Den här självstudien förutsätter att du har följande:

* En aktiv Azure-prenumeration. Du kan [Registrera dig för en kostnads fri utvärderings version](https://azure.microsoft.com/pricing/free-trial/).
* En befintlig Always on-tillgänglighets grupp lokalt. Mer information om tillgänglighets grupper finns i [Always on Availability groups](https://msdn.microsoft.com/library/hh510230.aspx).
* Anslutning mellan det lokala nätverket och det virtuella Azure-nätverket. Mer information om hur du skapar det här virtuella nätverket finns i [skapa en plats-till-plats-anslutning med hjälp av Azure Portal (klassisk)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="add-azure-replica-wizard"></a>Guiden Lägg till Azure-replik
I det här avsnittet lär du dig hur du använder **guiden Lägg till Azure-replik** för att utöka din lösning för tillgänglighets grupp för alltid till att inkludera Azure-repliker.

> [!IMPORTANT]
> **Guiden Lägg till Azure-replik** stöder bara virtuella datorer som skapats med den klassiska distributions modellen. Nya distributioner av virtuella datorer bör använda den nyare Resource Manager-modellen. Om du använder virtuella datorer med Resource Manager måste du manuellt lägga till den sekundära Azure-repliken med hjälp av Transact-SQL commmands (visas inte här). Den här guiden fungerar inte i Resource Manager-scenariot.

1. Från SQL Server Management Studio, expandera Always on-**tillgänglighets grupper** >  **med hög tillgänglighet** >  **[namn på din tillgänglighets grupp]** .
2. Högerklicka på **tillgänglighets repliker**och klicka sedan på **Lägg till replik**.
3. **Guiden Lägg till replik i tillgänglighets grupp** visas som standard. Klicka på **Nästa**.  Om du har valt alternativet **Visa inte den här sidan igen** längst ned på sidan under en tidigare lansering av guiden visas inte den här skärmen.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Du kommer att behöva ansluta till alla befintliga sekundära repliker. Du kan klicka på **Anslut...** bredvid varje replik eller så kan du klicka på **Anslut alla...** längst ned på skärmen. Efter autentisering klickar du på **Nästa** för att gå vidare till nästa skärm.
5. På sidan **Ange repliker** visas flera flikar högst upp: **Repliker**, **slut punkter**, **säkerhets kopierings inställningar**och **lyssnare**. Klicka på Lägg till Azure-replik på fliken repliker **...** Starta guiden Lägg till Azure-replik.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Välj ett befintligt hanterings certifikat för Azure från det lokala Windows-certifikatarkivet om du har installerat ett tidigare. Välj eller ange ID: t för en Azure-prenumeration om du har använt en tidigare. Du kan klicka på Hämta för att hämta och installera ett hanterings certifikat för Azure och ladda ned listan över prenumerationer med ett Azure-konto.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Du fyller i varje fält på sidan med värden som ska användas för att skapa den virtuella Azure-dator som ska vara värd för repliken.
   
   | Inställning | Beskrivning |
   | --- | --- |
   | **Avbildning** |Välj önskad kombination av OS och SQL Server |
   | **VM-storlek** |Välj storleken på den virtuella dator som passar dina affärs behov bäst |
   | **Namn på virtuell dator** |Ange ett unikt namn för den nya virtuella datorn. Namnet måste innehålla mellan 3 och 15 tecken, får bara innehålla bokstäver, siffror och bindestreck, och måste börja med en bokstav och sluta med antingen en bokstav eller en siffra. |
   | **VM-användarnamn** |Ange ett användar namn som ska bli administratörs kontot på den virtuella datorn |
   | **Administratörs lösen ord för virtuell dator** |Ange ett lösen ord för det nya kontot |
   | **Bekräfta lösen ord** |Bekräfta lösen ordet för det nya kontot |
   | **Virtual Network** |Ange det virtuella Azure-nätverket som den nya virtuella datorn ska använda. Mer information om virtuella nätverk finns i [Virtual Network översikt](../../../virtual-network/virtual-networks-overview.md). |
   | **Virtual Network undernät** |Ange det undernät för virtuellt nätverk som den nya virtuella datorn ska använda |
   | **Domän** |Bekräfta att det förifyllda värdet för domänen är korrekt |
   | **Domän användar namn** |Ange ett konto som finns i den lokala administratörs gruppen på de lokala klusternoderna |
   | **Lösenord** |Ange lösen ordet för domän användar namnet |
8. Verifiera distributions inställningarna genom att klicka på **OK** .
9. Juridiska villkor visas härnäst. Läs och klicka på **OK** om du samtycker till dessa villkor.
10. Sidan **Ange repliker** visas igen. Kontrol lera inställningarna för den nya Azure-replikenpå flikarna repliker, **slut punkter**och **säkerhets kopierings inställningar** . Ändra inställningarna så att de uppfyller dina affärs behov.  Mer information om de parametrar som finns på dessa flikar finns på [sidan Ange repliker (guiden Ny tillgänglighets grupp/Lägg till replik guiden)](https://msdn.microsoft.com/library/hh213088.aspx). Observera att lyssnare inte kan skapas med hjälp av fliken lyssnare för tillgänglighets grupper som innehåller Azure-repliker. Dessutom visas ett meddelande som anger att det inte stöds i Azure om en lyssnare redan har skapats innan du startar guiden. Vi ska titta på hur du skapar lyssnare i avsnittet **skapa en lyssnare för tillgänglighets grupp** .
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klicka på **Nästa**.
12. Välj den metod för data synkronisering som du vill använda på sidan **Välj inledande data synkronisering** och klicka på **Nästa**. I de flesta fall väljer du **fullständig datasynkronisering**. Mer information om metoder för data synkronisering finns i [Välj sidan för synkronisering av första data (alltid i guiderna för tillgänglighets grupper)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Granska resultaten på validerings sidan. Korrigera utestående problem och kör verifieringen igen om det behövs. Klicka på **Nästa**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Granska inställningarna på sidan **Sammanfattning** och klicka sedan på **Slutför**.
15. Etablerings processen börjar. När guiden har slutförts klickar du på **Stäng** för att avsluta guiden.

> [!NOTE]
> Guiden Lägg till Azure-replik skapar en loggfil i Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Logg filen kan användas för att felsöka misslyckade distributioner av Azure-replikering. Om guiden Miss lyckas med att köra någon åtgärd, återställs alla tidigare åtgärder, inklusive borttagning av den etablerade virtuella datorn.
> 
> 

## <a name="create-an-availability-group-listener"></a>Skapa en lyssnare för tillgänglighets grupp
När tillgänglighets gruppen har skapats bör du skapa en lyssnare för klienter för att ansluta till replikerna. Lyssnare dirigerar inkommande anslutningar till antingen den primära eller en skrivskyddad sekundär replik. Mer information om lyssnare finns i [Konfigurera en ILB-lyssnare för Always on Availability groups i Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Nästa steg
Förutom att använda **guiden Lägg till Azure-replik** för att utöka din ständigt tillgängliga tillgänglighets grupp till Azure kan du också flytta några SQL Server arbets belastningar helt till Azure. Information om hur du kommer igång finns i [etablering av en SQL Server virtuell dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på Azure-Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

