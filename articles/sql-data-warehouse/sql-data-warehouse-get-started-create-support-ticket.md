---
title: Så här skapar du ett support ärende
description: Så här skapar du ett support ärende i Azure Synapse Analytics.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 9623a878ea009ec7363501c09c324baa6d0fa7ad
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195723"
---
# <a name="how-to-create-a-support-ticket-for-azure-synapse-analytics"></a>Så här skapar du ett support ärende för Azure Synapse Analytics
Om du har problem med Azure Synapse Analytics kan du skapa ett support ärende så att teknik support teamet kan hjälpa dig.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende
1. Öppna [Azure-portalen](https://portal.azure.com/).
1. På startsidan klickar du på **hjälp + support**-fliken.
   
    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/main-page.png)

1. Granska [support avtalet för Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).
   
   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations** support tillhandahålls via [utvecklare](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier](https://azure.microsoft.com/support/plans/premier/) support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/). 
     
     Om du har en Premier-supportplan kan du även rapportera SQL Data Warehouse-relaterade problem via [Microsoft Premier-onlineportalen](https://premier.microsoft.com/). Se [support](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) avtalen för Azure om du vill veta mer om olika support avtal, inklusive omfattning, svars tider, priser osv.  Vanliga frågor och svar om support för Azure finns i vanliga frågor och [svar om support för Azure](https://azure.microsoft.com/support/faq/).
1. På sidan **Hjälp + Support** väljer du **ny support förfrågan**. Välj en typ av problem i den nedrullningsbara menyn. Fortsätt sedan att fylla i informationen på fliken **grundläggande** . Ange en **Sammanfattning** av problemet och välj sedan en **problem typ** på menyn och välj Spara.

    ![Hjälp + support](./media/sql-data-warehouse-get-started-create-support-ticket/issue-type.png)

   > [!NOTE]
   > Varje SQL Server (t.ex. myserver.database.windows.net) har som standard en **DTU-kvot** på 45 000. Kvoten är helt enkelt en säkerhetsgräns. Du kan öka din kvot genom att skapa en supportbiljett och välja *Kvot* som typ av begäran. För att beräkna dina DTU-behov multiplicerar du 7,5 med den totala [DWU](sql-data-warehouse-overview-what-is.md) som behövs. Om du till exempel skulle vilja vara värd för två DW6000 på en SQL Server, bör du begära en DTU-kvot på 90 000.  Du kan visa din aktuella DTU-förbrukning från SQL Server-bladet i portalen. Både pausade och inte pausade databaser räknas i förhållande till DTU-kvoten. 
   > 

1. Du kan se lösningar som kan hjälpa dig att lösa problemet. Om lösningarna som presenteras inte löser problemet väljer du **Nästa: information**. Skicka information om ditt problem och din kontakt information. Välj **Nästa: granska + skapa**
![information](./media/sql-data-warehouse-get-started-create-support-ticket/details.png)

    
1. Granska informationen och välj **skapa** längst ned i formuläret för att skicka in support förfrågan.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende
När du har skickat in support förfrågan kontaktar support teamet för Azure. Du kan kontrollera status och detaljer för din begäran genom att klicka på **Alla supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Andra resurser
Du kan också ansluta till SQL Data Warehouse communityn på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) eller via [Azure SQL Data Warehouse MSDN-forumet](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

 
