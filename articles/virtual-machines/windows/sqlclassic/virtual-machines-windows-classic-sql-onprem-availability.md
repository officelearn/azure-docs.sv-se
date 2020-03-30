---
title: Utöka lokala alltid på tillgänglighetsgrupper till Azure | Microsoft-dokument
description: Den här självstudien använder resurser som skapats med den klassiska distributionsmodellen och beskriver hur du använder guiden Lägg till replik i SQL Server Management Studio (SSMS) för att lägga till en replik för alltid på tillgänglighet i Azure.
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
ms.openlocfilehash: 4521c2c112c93e83144cfc84d600208817b2ccac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978053"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Utöka lokala AlwaysOn-tillgänglighetsgrupper till Azure
Alltid på tillgänglighetsgrupper ger hög tillgänglighet för grupper av databaser genom att lägga till sekundära repliker. Dessa repliker tillåter misslyckas över databaser i händelse av ett fel. Dessutom kan de användas för att avlasta läsarbetsbelastningar eller säkerhetskopieringsuppgifter.

Du kan utöka lokala tillgänglighetsgrupper till Microsoft Azure genom att etablera en eller flera virtuella Azure-datorer med SQL Server och sedan lägga till dem som repliker i dina lokala tillgänglighetsgrupper.

Den här självstudien förutsätter att du har följande:

* En aktiv Azure-prenumeration. Du kan [registrera dig för en gratis provperiod](https://azure.microsoft.com/pricing/free-trial/).
* En befintlig alltid på tillgänglighetsgrupp lokalt. Mer information om tillgänglighetsgrupper finns [i Alltid på tillgänglighetsgrupper](https://msdn.microsoft.com/library/hh510230.aspx).
* Anslutning mellan det lokala nätverket och ditt virtuella Azure-nätverk. Mer information om hur du skapar det här virtuella nätverket finns i [Skapa en plats-till-plats-anslutning med Azure-portalen (klassiskt)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md).

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="add-azure-replica-wizard"></a>Guiden Lägg till Azure-replik
I det här avsnittet visas hur du använder **guiden Lägg till Azure-replik** för att utöka lösningen för alltid på tillgänglighetsgrupp till att omfatta Azure-repliker.

> [!IMPORTANT]
> **Guiden Lägg till Azure-replik** stöder endast virtuella datorer som skapats med den klassiska distributionsmodellen. Nya VM-distributioner bör använda den nyare Resource Manager-modellen. Om du använder virtuella datorer med Resource Manager måste du manuellt lägga till den sekundära Azure-repliken med Transact-SQL-kommandon (visas inte här). Den här guiden fungerar inte i Resource Manager-scenariot.

1. I SQL Server Management Studio expanderar du alltid på**tillgänglighetsgrupper** >  **med hög tillgänglighet** > [Namnet på**din tillgänglighetsgrupp]**.
2. Högerklicka på **Tillgänglighetsrepliker**och klicka sedan på **Lägg till replik**.
3. Som standard visas **guiden Lägg till replik i tillgänglighet.** Klicka på **Nästa**.  Om du har valt alternativet **Visa inte den här sidan igen** längst ned på sidan under en tidigare start av den här guiden visas inte den här skärmen.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. Du måste ansluta till alla befintliga sekundära repliker. Du kan klicka på **Anslut ...** bredvid varje replik eller så kan du klicka på **Anslut alla ...** längst ned på skärmen. När du har autentisering klickar du på **Nästa** för att gå vidare till nästa skärm.
5. På sidan **Ange repliker** visas flera flikar överst: **Repliker**, **Slutpunkter**, **Inställningar för säkerhetskopiering**och **Lyssnare**. Klicka på **Lägg till Azure-replik på** fliken **Repliker...** för att starta guiden Lägg till Azure-replik.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. Välj ett befintligt Azure Management-certifikat från det lokala Windows-certifikatarkivet om du har installerat ett tidigare. Välj eller ange id för en Azure-prenumeration om du har använt en tidigare. Du kan klicka på Hämta för att hämta och installera ett Azure Management-certifikat och hämta listan över prenumerationer med ett Azure-konto.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. Du fyller i varje fält på sidan med värden som ska användas för att skapa den virtuella Azure-datorn (VM) som ska vara värd för repliken.
   
   | Inställning | Beskrivning |
   | --- | --- |
   | **Bild** |Välj önskad kombination av OS och SQL Server |
   | **Vm-storlek** |Välj storleken på den virtuella datorn som bäst passar dina affärsbehov |
   | **VM-namn** |Ange ett unikt namn för den nya virtuella datorn. Namnet måste innehålla mellan 3 och 15 tecken, kan bara innehålla bokstäver, siffror och bindestreck och måste börja med en bokstav och sluta med antingen en bokstav eller ett nummer. |
   | **Vm-användarnamn** |Ange ett användarnamn som ska bli administratörskontot på den virtuella datorn |
   | **Lösenord för VM-administratör** |Ange ett lösenord för det nya kontot |
   | **Bekräfta lösenord** |Bekräfta lösenordet för det nya kontot |
   | **Virtuellt nätverk** |Ange det virtuella Azure-nätverk som den nya virtuella datorn ska använda. Mer information om virtuella nätverk finns i [Översikt över virtuella nätverk](../../../virtual-network/virtual-networks-overview.md). |
   | **Undernät för virtuellt nätverk** |Ange det virtuella nätverksundernät som den nya virtuella datorn ska använda |
   | **Domain** |Bekräfta att det förifyllda värdet för domänen är korrekt |
   | **Domännamn för domän** |Ange ett konto i gruppen lokala administratörer på de lokala klusternoderna |
   | **Lösenord** |Ange lösenordet för domännamnet för domänen |
8. Klicka på **OK** för att validera distributionsinställningarna.
9. Juridiska villkor visas nästa. Läs och klicka på **OK** om du godkänner dessa villkor.
10. Sidan **Ange repliker** visas igen. Verifiera inställningarna för den nya Azure-repliken på **flikarna Repliker,** **slutpunkter**och **inställningar för säkerhetskopiering.** Ändra inställningar för att uppfylla dina affärskrav.  Mer information om parametrarna på dessa flikar finns i [Ange replikersida (guiden Ny tillgänglighetsgrupp/Guiden Lägg till replik)](https://msdn.microsoft.com/library/hh213088.aspx). Observera att lyssnare inte kan skapas med hjälp av fliken Lyssnare för tillgänglighetsgrupper som innehåller Azure-repliker. Om en lyssnare redan har skapats innan guiden startas får du dessutom ett meddelande om att den inte stöds i Azure. Vi kommer att titta på hur du skapar lyssnare i avsnittet **Skapa en tillgänglighetsgrupplyssnare.**
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. Klicka på **Nästa**.
12. Markera den datasynkroniseringsmetod som du vill använda på sidan **Välj initial datasynkronisering** och klicka på **Nästa**. I de flesta scenarier väljer du **Fullständig datasynkronisering**. Mer information om datasynkroniseringsmetoder finns i [Välj inledande datasynkroniseringssida (alltid på tillgänglighetsgruppguider)](https://msdn.microsoft.com/library/hh231021.aspx).
13. Granska resultaten på sidan **Validering.** Korrigera kvarstående problem och kör valideringen igen om det behövs. Klicka på **Nästa**.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. Granska inställningarna på sidan **Sammanfattning** och klicka sedan på **Slutför**.
15. Etableringsprocessen börjar. När guiden är klar klickar du på **Stäng** för att avsluta guiden.

> [!NOTE]
> Guiden Lägg till Azure-replik skapar en loggfil i Användare\Användarnamn\AppData\Lokal\SQL Server\AddReplicaWizard. Den här loggfilen kan användas för att felsöka misslyckade Azure-replikdistributioner. Om guiden inte kör någon åtgärd återställs alla tidigare åtgärder, inklusive att ta bort den etablerade virtuella datorn.
> 
> 

## <a name="create-an-availability-group-listener"></a>Skapa en lyssnare i tillgänglighetsgruppen
När tillgänglighetsgruppen har skapats bör du skapa en lyssnare för klienter som kan ansluta till replikerna. Lyssnare dirigerar inkommande anslutningar till antingen den primära eller en skrivskyddad sekundär replik. Mer information om lyssnare finns i [Konfigurera en ILB-lyssnare för alltid på tillgänglighetsgrupper i Azure](../classic/ps-sql-int-listener.md).

## <a name="next-steps"></a>Nästa steg
Förutom att använda **guiden Lägg till Azure-replik** för att utöka din grupp alltid på tillgänglighet till Azure, kan du också flytta vissa SQL Server-arbetsbelastningar helt till Azure. Information om hur du kommer igång finns [i Etablering av en virtuell SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Mer information om hur du kör SQL Server i virtuella Azure-datorer finns i [SQL Server på virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

