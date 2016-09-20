<properties
   pageTitle="Så här skapar du ett supportärende för SQL Data Warehouse | Microsoft Azure"
   description="Så här skapar du ett supportärende i Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="sonyam;barbkess;sonyama"/>

# Så här skapar du ett supportärende för SQL Data Warehouse
 
Om du har problem med ditt SQL Data Warehouse, ber vi dig skapa ett supportärende så att vårat teknikerteam kan hjälpa dig.

## Skapa ett supportärende

1. Öppna [Azure-portalen][].

2. På startsidan klickar du på **hjälp + support**-panelen.

    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. På Hjälp + Support-bladet, klickar du på **skapa supportbegäran**.

    ![Ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Välj **typ av begäran**.

    ![Typ av begäran](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Varje SQL Server (t.ex. myserver.database.windows.net) har som standard en **DTU-kvot** på 45 000. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom att skapa en supportbiljett och välja *Kvot* som typ av begäran. Om du vill beräkna DTU-behoven behöver du veta att varje 100 [DWU][] SQL Data Warehouse förbrukar 750 DTU. Därför medför standardkvoten på 45 000 att du kan skapa upp till en DW6000 (45 000/750) eller flera mindre databaser med mindre DWU. Om du som exempel skulle vilja vara värd för två DW6000 på en SQL Server, bör du begära en DTU-kvot på 90 000.  Du kan visa din aktuella DTU-förbrukning från SQL Server-bladet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. 

5. Välj den **prenumeration** som är värd för databasen som har problemen du ska rapportera.

    ![Prenumeration](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Välj **SQL Data Warehouse** som resursen.

    ![Resurs](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Välj din [Azure-supportplan][].

    - Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
    - **Reparations**-support tillhandahålls via supporttypen [Developer][], [Standard][], [Professional Direct][] eller [Premier][]. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
    - **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct][] och [Premier][]. 
    
    Om du har en Premier-supportplan kan du även rapportera SQL Data Warehouse-relaterade problem via [Microsoft Premier-onlineportalen][].  Se [Azure-supportplaner][Azure-supportplan] för mer information om olika supportplaner, inklusive omfattning, svarstider, prissättning, osv.  Vanliga frågor om Azure-supporten finns i [Vanliga frågor och svar om support för Azure][].  

    ![Supportplan](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Välj **problemtyp** och **kategori**.

    ![Problemtyp-kategori](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Beskriv problemet och välj vilken inverkan det har på verksamheten.

    ![Problembeskrivning](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Din **kontaktinformation** för det här supportärendet fylls i automatiskt. Uppdatera den vid behov.

    ![Kontaktuppgifter](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Klicka på **skapa** för att skicka in supportbegäran.


## Övervaka ett supportärende

När du har skickat in supportbegäran, kommer Azure-teamet att kontakta dig. Du kan kontrollera status och detaljer för din begäran genom att klicka på **hantera supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## Andra resurser

Dessutom kan du ansluta till SQL Data Warehouse-communityn i [Stack Overflow][] eller på [Azure SQL Data Warehouse MSDN-forum][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure-portalen]: https://portal.azure.com/
[Azure-supportplan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Vanliga frågor och svar om support för Azure]: https://azure.microsoft.com/support/faq/
[Microsoft Premier-onlineportalen]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN-forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/




<!--HONumber=sep16_HO1-->


