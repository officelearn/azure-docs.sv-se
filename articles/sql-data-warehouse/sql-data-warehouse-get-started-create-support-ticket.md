---
title: Så här skapar du ett support ärende
description: Så här skapar du ett supportärende i Azure SQL Data Warehouse
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717835"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Så här skapar du ett supportärende för SQL Data Warehouse
Om du har problem med din SQL Data Warehouse kan du skapa ett support ärende så att support teamet kan hjälpa dig.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende
1. Öppna [Azure-portalen](https://portal.azure.com/).
2. På startsidan klickar du på **hjälp + support**-fliken.
   
    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. På Hjälp + Support-bladet, klickar du på **Ny supportbegäran** och fyller i bladet **Allmänt**.

   Välj din [Azure-supportplan](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations** support tillhandahålls via [utvecklare](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier](https://azure.microsoft.com/support/plans/premier/) support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Om du har en Premier-supportplan kan du även rapportera SQL Data Warehouse-relaterade problem via [Microsoft Premier-onlineportalen](https://premier.microsoft.com/). Se [support](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) avtalen för Azure om du vill veta mer om olika support avtal, inklusive omfattning, svars tider, priser osv.  Vanliga frågor och svar om support för Azure finns i vanliga frågor och [svar om support för Azure](https://azure.microsoft.com/support/faq/).  
        
     ![Bladet allmänt](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![Bladet allmänt1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Fyll i bladet **Problem**.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Varje SQL Server (t.ex. myserver.database.windows.net) har som standard en **DTU-kvot** på 45 000. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom att skapa en supportbiljett och välja *Kvot* som typ av begäran. För att beräkna dina DTU-behov multiplicerar du 7,5 med den totala [DWU](sql-data-warehouse-overview-what-is.md) som behövs. Om du till exempel skulle vilja vara värd för två DW6000 på en SQL Server, bör du begära en DTU-kvot på 90 000.  Du kan visa din aktuella DTU-förbrukning från SQL Server-bladet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. 
   > 
   > 
   
5. Fyll i dina **kontaktuppgifter**.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Klicka på **skapa** för att skicka in supportbegäran.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende
När du har skickat in support förfrågan kontaktar support teamet för Azure. Du kan kontrollera status och detaljer för din begäran genom att klicka på **Alla supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Andra resurser
Du kan också ansluta till SQL Data Warehouse communityn på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) eller via [Azure SQL Data Warehouse MSDN-forumet](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
