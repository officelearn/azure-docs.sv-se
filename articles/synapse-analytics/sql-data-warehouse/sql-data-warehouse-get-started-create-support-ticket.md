---
title: Begär kvothöjningar och få stöd
description: Så här skapar du en supportbegäran i Azure-portalen för Azure Synapse Analytics. Begär kvot ökar eller få problemlösningsstöd.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350897"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Begär kvot ökar och få stöd för Azure Synapse Analytics

I den här artikeln beskrivs hur du skickar in en supportbiljett i Azure-portalen för Azure Synapse Analytics. Med den här processen kan du begära en kvotökning eller skicka in en teknisk supportbegäran för den tekniska supportteamet.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende

Följ följande steg för att skapa en ny supportbegäran från Azure-portalen för Azure Synapse Analytics.

1. På [Portalmenyn i Azure](https://portal.azure.com) väljer du **Stöd för Hjälp +**.

   ![Supportlänken hjälp +](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Välj **Ny supportbegäran i** **Hjälp + support**.

    ![Skapa en ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Granska din [Azure-supportplan](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Support för break-fix** tillhandahålls via [supporten utvecklare,](https://azure.microsoft.com/support/plans/developer/) [standard,](https://azure.microsoft.com/support/plans/standard/) [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier.](https://azure.microsoft.com/support/plans/premier/) Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/).

   Om du har en Premier-supportplan kan du också rapportera Azure Synapse Analytics-problem på [Microsoft Premier onlineportalen](https://premier.microsoft.com/). Se [Azure-supportplaner](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) om du vill veta mer om de olika supportplanerna, inklusive omfattning, svarstider, priser osv.  Vanliga frågor och svar om Azure-support finns i Vanliga frågor och svar om [Azure-support](https://azure.microsoft.com/support/faq/).

1. Välj lämplig ärendetyp för **ärendetyp.** Om du vill lösa problem väljer du **Teknisk**. För begäranden om kvotökning väljer du **Tjänst- och prenumerationsgränser (kvoter)**.

   ![Välj en ärendetyp](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Den här resten av den här artikeln fokuserar på begäranden om kvotökning. Men du kan också välja **Teknisk** här för problemlösningssupportbegäranden. Om du väljer **Teknisk**uppmanas du att ange en sammanfattning och sedan identifiera en problemtyp genom att välja **Välj problemtyp**. Du kan se lösningar som hjälper dig att lösa problemet. Om de lösningar som presenteras inte löser problemet väljer du **Nästa:Information** och fyller i formuläret för att skicka supportbiljetten.

1. För begäranden om kvotökning väljer du **Azure Synapse Analytics** för **typen Kvot**. Välj sedan **Nästa: Lösningar >>**.

   ![Välj en kvottyp](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. I fönstret **Information** väljer du **Ange information** om du vill ange ytterligare information.

   ![Länken "Ge information"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typer av kvotbegäranden

Om du klickar på **Ange information** visas fönstret **Kvotinformation** som gör att du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika kvotbegäranden som är tillgängliga för Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Data warehouseenheter (DWUs) per server

Följ följande steg för att begära en ökning av DWUs per server.

1. Välj **DTUs (Data Warehouse Units) per** serverkvottyp.

1. Välj den resurs som ska riktas i listan **Resurs.**

1. I fältet **Begär kvot** anger du den nya DWU-gränsen som du begär.

   ![Information om DWU-kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servrar per prenumeration

Följ följande steg för att begära en ökning av antalet servrar per prenumeration.

1. Välj **kvottypen Servrar per prenumeration.**

1. Välj den Azure-region som ska användas i listan **Plats.** Kvoten är per prenumeration i varje region.

1. I fältet **Ny kvot** anger du din begäran om det maximala antalet servrar i den regionen.

   ![Information om kvot för servrar](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Aktivera prenumerationsåtkomst till en region

Vissa erbjudandetyper är inte tillgängliga i alla regioner. Du kan se ett fel som följande:

`This location is not available for subscription`

Om din prenumeration behöver åtkomst i en viss region använder du alternativet **Annan kvotbegäran för** att begära åtkomst. I din begäran anger du den erbjudande- och SKU-information som du vill aktivera för regionen. Information om hur du utforskar alternativen för erbjudandet och SKU finns i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Övriga kvotuppgifter](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Skicka din förfrågan

Det sista steget är att fylla i återstående information om din SQL Database-supportbegäran. Välj sedan **Nästa: Granska + skapa>>** och när du har granskat information om begäran klickar du på **Skapa** för att skicka begäran.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende

När du har skickat supportbegäran kontaktar Azure-supportteamet dig. Du kan kontrollera status och detaljer för din begäran genom att klicka på **Alla supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Andra resurser

Du kan också ansluta till Azure Synapse Analytics-communityn på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) eller via [AZURE SQL Data Warehouse MSDN-forumet](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/).

