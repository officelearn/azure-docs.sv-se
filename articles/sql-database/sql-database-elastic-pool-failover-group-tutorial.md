---
title: 'Självstudier: Lägga till en Azure SQL Database elastisk pool i en redundans-grupp | Microsoft Docs'
description: Lägg till en Azure SQL Database elastisk pool i en failover-grupp med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 0507b3cb64b4b12bac92cc6bc90120ab4ec56dee
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568750"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Självstudier: Lägga till en Azure SQL Database elastisk pool i en failover-grupp

Konfigurera en failover-grupp för en Azure SQL Database elastisk pool och testa redundans med hjälp av Azure Portal.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> - Skapa en Azure SQL Database enskild databas.
> - Lägg till den enkla databasen i en elastisk pool. 
> - Skapa en [failover-grupp](sql-database-auto-failover-group.md) för en elastisk pool mellan två logiska SQL-servrar.
> - Redundanstest.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha: 

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.


## <a name="1---create-a-single-database"></a>1 – Skapa en enskild databas 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 – Lägg till en enkel databas i elastisk pool

1. Välj **skapa en resurs** i det övre vänstra hörnet av [Azure Portal](https://portal.azure.com).
1. Skriv `elastic pool` i sökrutan, tryck på RETUR, Välj ikonen **SQL Elastic Database-pool** och välj sedan **skapa**. 

    ![Välj elastisk pool från Marketplace](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Konfigurera den elastiska poolen med följande värden:
   - **Namn på**: Ange ett unikt namn för den elastiska poolen, till `myElasticPool`exempel. 
   - **Prenumeration**: Välj din prenumeration från listrutan.
   - **ResourceGroup**: Välj `myResourceGroup` i list rutan, resurs gruppen du skapade i avsnitt 1. 
   - **Server**: Välj den server som du skapade i avsnitt 1 i list rutan.  

       ![Skapa en ny server för elastisk pool](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + Storage**: Välj **Konfigurera elastisk pool** för att konfigurera din beräkning, lagring och Lägg till din enda databas i den elastiska poolen. På fliken **Inställningar för pool** lämnar du standardvärdet Gen5, med 2 virtuella kärnor och 32 GB. 

1. Välj fliken **databaser** på sidan **Konfigurera** och välj sedan att **lägga till databas**. Välj den databas som du skapade i avsnitt 1 och välj sedan **tillämpa** för att lägga till den i den elastiska poolen. Välj **tillämpa** igen för att tillämpa inställningarna för elastisk pool och Stäng sidan **Konfigurera** . 

    ![Lägg till SQL DB i elastisk pool](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Välj **Granska + skapa** för att granska inställningarna för elastisk pool och välj sedan **skapa** för att skapa en elastisk pool. 


## <a name="3---create-the-failover-group"></a>3 – skapa redundans gruppen 
I det här steget ska du skapa en [redundans grupp](sql-database-auto-failover-group.md) mellan en befintlig Azure SQL-Server och en ny Azure SQL-Server i en annan region. Lägg sedan till den elastiska poolen i gruppen redundans. 


1. Välj **alla tjänster** i det övre vänstra hörnet av [Azure Portal](https://portal.azure.com). 
1. Skriv `sql servers` i sökrutan. 
1. Valfritt Välj stjärn ikonen bredvid SQL-servrar för att skapa **SQL-servrar** och lägga till dem i det vänstra navigerings fönstret. 
    
    ![Hitta SQL-servrar](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Välj **SQL-servrar** och välj den server som du skapade i avsnitt 1.
1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan **Lägg till grupp** för att skapa en ny grupp för redundans. 

    ![Lägg till ny redundans grupp](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)

1. På sidan **redundans** anger eller väljer du följande värden och väljer sedan **skapa**:
    - **Namn på redundans grupp**: Ange ett unikt namn för redundans, till exempel `failovergrouptutorial`. 
    - **Sekundär server**: Välj alternativet för att *Konfigurera nödvändiga inställningar* och välj sedan att **skapa en ny server**. Alternativt kan du välja en redan befintlig server som den sekundära servern. När du har angett följande värden väljer du **Välj**. 
        - **Servernamn**: Ange ett unikt namn på den sekundära servern, till exempel `mysqlsecondary`. 
        - **Inloggning för serveradministratör**: Bastyp`azureuser`
        - **Lösenord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
        - **Plats**: Välj en plats i list rutan, till exempel USA, östra 2. Den här platsen kan inte vara samma plats som den primära servern.

       > [!NOTE]
       > Inställningarna för Server inloggning och brand vägg måste matcha den primära servern. 
    
       ![Skapa en sekundär server för redundans gruppen](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. När du har valt en sekundär server blir **databaserna i grupp** alternativet olåsta. Markera den för att **välja databaser som ska läggas till** och välj sedan den elastiska pool som du skapade i avsnitt 2. En varning bör visas, så att du snabbt kan skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK** för att skapa den elastiska poolen på den sekundära servern. 
        
    ![Lägg till SQL DB i redundans gruppen](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        
1. Välj **Välj** om du vill tillämpa inställningarna för elastisk pool på gruppen växling vid fel och välj sedan **skapa** för att skapa din grupp för redundans. Om du lägger till den elastiska poolen i gruppen redundans startas geo-replikeringen automatiskt. 


## <a name="4---test-failover"></a>4 – redundanstest 
I det här steget kommer du inte att kunna redundansväxla gruppen till den sekundära servern och sedan växla tillbaka med Azure Portal. 

1. Gå till **SQL** Server-servern inom [Azure Portal](https://portal.azure.com). 
1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan den grupp för växling vid fel som du skapade i avsnitt 2. 
  
   ![Välj gruppen redundans från portalen](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär. 
1. Välj **redundans** från åtgärds fönstret för att Redundansväxla din redundansväxling som innehåller den elastiska poolen. 
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas bort. 

   ![Redundansväxla din failover-grupp som innehåller din SQL-databas](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansväxlingen lyckades måste de två servrarna ha växlade roller. 
1. Välj **redundans** igen om du vill att redundans-gruppen ska växla tillbaka till de ursprungliga inställningarna. 

## <a name="clean-up-resources"></a>Rensa resurser 
Rensa resurser genom att ta bort resurs gruppen. 

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com).
1. Välj **ta bort resurs grupp** för att ta bort alla resurser i gruppen, samt själva resurs gruppen. 
1. Skriv namnet på resurs gruppen `myResourceGroup`, i text rutan och välj sedan **ta bort** för att ta bort resurs gruppen.  


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till en Azure SQL Database enkel databas i en failover-grupp och testat redundansväxlingen. Du har lärt dig att:

> [!div class="checklist"]
> - Skapa en Azure SQL Database enskild databas.
> - Lägg till den enkla databasen i en elastisk pool. 
> - Skapa en [failover-grupp](sql-database-auto-failover-group.md) för en elastisk pool mellan två logiska SQL-servrar.
> - Redundanstest.

Gå vidare till nästa självstudie om hur du migrerar med DMS.

> [!div class="nextstepaction"]
> [Självstudier: Migrera SQL Server till en poolad databas med DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
