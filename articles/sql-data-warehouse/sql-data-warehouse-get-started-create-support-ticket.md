---
title: "Så här skapar du ett supportärende för SQL Data Warehouse | Microsoft Docs"
description: "Så här skapar du ett supportärende i Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
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
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.lasthandoff: 02/11/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Så här skapar du ett supportärende för SQL Data Warehouse
Om du har problem med din SQL Data Warehouse, ber vi dig skapa ett supportärende så att våra tekniker kan hjälpa dig.

> [!NOTE] 
> Från och med 20/12/2016 stämmer inte hälsokontrollen för resurser i Azure-portalen. Vi arbetar för att åtgärda problemet. 


## <a name="create-a-support-ticket"></a>Skapa ett supportärende
1. Öppna [Azure-portalen][Azure portal].
2. På startsidan klickar du på **hjälp + support**-panelen.
   
    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. På Hjälp + Support-bladet, klickar du på **skapa supportbegäran**.
   
    ![Ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. Välj **typ av begäran**.
   
    ![Typ av begäran](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > Varje SQL Server (t.ex. myserver.database.windows.net) har som standard en **DTU-kvot** på 45 000. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom att skapa en supportbiljett och välja *Kvot* som typ av begäran. För att beräkna dina DTU-behov, multiplicerar du 7,5 med det totala antalet [DWU:er][DWU] du behöver. Om du till exempel skulle vilja vara värd för två DW6000 på en SQL Server, bör du begära en DTU-kvot på 90 000.  Du kan visa din aktuella DTU-förbrukning från SQL Server-bladet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. 
   > 
   > 
5. Välj den **prenumeration** som är värd för databasen som har problemen du ska rapportera.
   
    ![Prenumeration](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. Välj **SQL Data Warehouse** som resursen.
   
    ![Resurs](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. Välj din [Azure-supportplan][Azure support plan].
   
   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations**-support tillhandahålls via [Utvecklare][Developer], [Standard][Standard], [Professional Direct][Professional Direct] eller [Premier][Premier] support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct][Professional Direct] och [Premier][Premier]. 
     
     Om du har en Premier-supportplan, kan du även rapportera SQL Data Warehouse-relaterade problem på [Microsoft Premier-onlineportalen][Microsoft Premier online portal].  Se [Azure-supportplaner][Azure support plan] för att läsa mer om de olika supportplanerna, inklusive omfattning, svarstider, prissättning, osv.  Vanliga frågor och svar om Azure-support finns på [Vanliga frågor och svar om Azure-support][Azure support FAQs].  
     
     ![Supportplan](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. Välj **problemtyp** och **kategori**. I det här exemplet har vi valt ”Verktyg” som problemtyp och ”Klientverktyg” som kategori. 
   
    ![Problemtyp-kategori](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. Beskriv problemet och välj vilken inverkan det har på verksamheten.
   
    ![Problembeskrivning](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. Din **kontaktinformation** för det här supportärendet fylls i automatiskt. Uppdatera den vid behov.
    
    ![Kontaktuppgifter](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. Klicka på **skapa** för att skicka in supportbegäran.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende
När du har skickat in supportbegäran, kommer Azure-teamet att kontakta dig. Du kan kontrollera status och detaljer för din begäran genom att klicka på **hantera supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

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


