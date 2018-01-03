---
title: "Så här skapar du ett supportärende för SQL Data Warehouse | Microsoft Docs"
description: "Så här skapar du ett supportärende i Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: 66aa12431e39ce295ce76411693bd20535a5c70c
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Så här skapar du ett supportärende för SQL Data Warehouse
Om du har problem med din SQL Data Warehouse, skapar du ett supportärende så att våra tekniker kan hjälpa dig.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende
1. Öppna [Azure-portalen][Azure portal].
2. På startsidan klickar du på **hjälp + support**-fliken.
   
    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. På Hjälp + Support-bladet, klickar du på **Ny supportbegäran** och fyller i bladet **Allmänt**.

   Välj din [Azure-supportplan][Azure support plan].
   
   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations**-support tillhandahålls via [Utvecklare][Developer], [Standard][Standard], [Professional Direct][Professional Direct] eller [Premier][Premier] support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct][Professional Direct] och [Premier][Premier]. 
     
     Om du har en Premier-supportplan, kan du även rapportera SQL Data Warehouse-relaterade problem på [Microsoft Premier-onlineportalen][Microsoft Premier online portal].  Se [Azure-supportplaner][Azure support plan] för att läsa mer om de olika supportplanerna, inklusive omfattning, svarstider, prissättning, osv.  Vanliga frågor och svar om Azure-support finns på [Vanliga frågor och svar om Azure-support][Azure support FAQs].  
        
    ![Bladet allmänt](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
    ![Bladet allmänt1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. Fyll i bladet **Problem**.
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > Varje SQL Server (t.ex. myserver.database.windows.net) har som standard en **DTU-kvot** på 45 000. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom att skapa en supportbiljett och välja *Kvot* som typ av begäran. För att beräkna dina DTU-behov, multiplicerar du 7,5 med det totala antalet [DWU:er][DWU] du behöver. Om du till exempel skulle vilja vara värd för två DW6000 på en SQL Server, bör du begära en DTU-kvot på 90 000.  Du kan visa din aktuella DTU-förbrukning från SQL Server-bladet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. 
   > 
   > 
   
5. Fyll i dina **kontaktuppgifter**.
![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. Klicka på **skapa** för att skicka in supportbegäran.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende
När du har skickat in supportbegäran, kommer Azure-teamet att kontakta dig. Du kan kontrollera status och detaljer för din begäran genom att klicka på **Alla supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>Andra resurser
Du kan dessutom ansluta till SQL Data Warehouse-communityn på [Stack Overflow][Stack Overflow] eller på [Azure SQL Data Warehouse MSDN-forumet][Azure SQL Data Warehouse MSDN forum].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

