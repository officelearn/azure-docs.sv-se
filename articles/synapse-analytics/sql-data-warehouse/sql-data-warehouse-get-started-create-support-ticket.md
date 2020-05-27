---
title: Begär ande kvot ökar och får support
description: Så här skapar du en support förfrågan i Azure Portal för Azure Synapse Analytics. Begär ande kvot ökar eller får support för problem matchning.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835469"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Begär ande kvot ökar och får support för Azure Synapse Analytics

Den här artikeln beskriver hur du skickar in ett support ärende i Azure Portal för Azure Synapse Analytics. Med den här processen kan du begära en kvot ökning eller skicka en begäran om teknisk support för support teamet för tekniska support.

## <a name="create-a-support-ticket"></a>Skapa ett supportärende

Använd följande steg för att skapa en ny supportbegäran från Azure Portal för Azure Synapse Analytics.

1. På [Azure Portal](https://portal.azure.com) -menyn väljer du **Hjälp + Support**.

   ![Länken Hjälp + Support](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. I **Hjälp + Support**väljer du **ny supportbegäran**.

    ![Skapa en ny supportbegäran](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Granska [support avtalet för Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Stöd för **fakturerings-, kvot- och prenumerationhantering** finns tillgängligt på alla supportnivåer.
   * **Reparations** support tillhandahålls via [utvecklare](https://azure.microsoft.com/support/plans/developer/), [standard](https://azure.microsoft.com/support/plans/standard/), [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/)eller [Premier](https://azure.microsoft.com/support/plans/premier/) support. Reparationsärenden är problem som kunder upplever när de använder Azure och där det rimligen kan antas att Microsoft orsakade problemet.
   * **Utvecklarhandledning** och **rådgivningstjänster** finns tillgängliga på supportnivåerna [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) och [Premier](https://azure.microsoft.com/support/plans/premier/).

   Om du har ett Premier support-avtal kan du även rapportera problem med Azure Synapse Analytics på [Microsoft Premier Online-portalen](https://premier.microsoft.com/). Se [support](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) avtalen för Azure om du vill veta mer om olika support avtal, inklusive omfattning, svars tider, priser osv.  Vanliga frågor och svar om support för Azure finns i vanliga frågor och [svar om support för Azure](https://azure.microsoft.com/support/faq/).

1. För **typ av problem**väljer du lämplig typ av problem. För problem med att lösa problemet väljer du **teknisk**. För förfrågningar om ökad kvot väljer du **tjänst-och prenumerations gränser (kvoter)**.

   ![Välj en typ av problem](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > Den här återstoden av den här artikeln fokuserar på kvot-öka förfrågningar. Men du kan också välja **teknisk** här för förfrågningar om problemlösnings support. Om du väljer **teknisk**uppmanas du att ange en sammanfattning och sedan identifiera en problem typ genom att välja **Välj typ av problem**. Du kan se lösningar som kan hjälpa dig att lösa problemet. Om lösningarna som presenteras inte löser problemet väljer du **Nästa: information** och fyller i formuläret för att skicka in support ärendet.

1. Välj **Azure Synapse Analytics** som **kvot typ**för förfrågningar om ökad kvot. Välj sedan **Nästa: lösningar >>**.

   ![Välj en kvot typ](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. I **informations** fönstret väljer du **Ange information** för att ange ytterligare information.

   ![Länken "Tillhandahåll information"](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typer av kvot förfrågningar

Om du klickar på **Ange information** visas fönstret **kvot Detaljer** där du kan lägga till ytterligare information. I följande avsnitt beskrivs de olika kvot begär Anden som är tillgängliga för Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Informations lager enheter (DWU: er) per server

Använd följande steg för att begära en ökning av DWU: er per server.

1. Välj **data lager enheter (DTU: er) per server** kvot typ.

1. I listan **resurs** väljer du den resurs som ska riktas mot målet.

1. I fältet **begär kvot** anger du den nya DWU-gränsen som du begär.

   ![Information om DWU-kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servrar per prenumeration

Använd följande steg för att begära en ökning av antalet servrar per prenumeration.

1. Välj kvot typ för **servrar per prenumeration** .

1. I listan **plats** väljer du den Azure-region som du vill använda. Kvoten är per prenumeration i varje region.

1. I fältet **ny kvot** anger du din begäran om maximalt antal servrar i den regionen.

   ![Information om servrars kvot](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Aktivera prenumerations åtkomst till en region

Vissa erbjudande typer är inte tillgängliga i varje region. Du kan se ett fel meddelande, till exempel följande:

`This location is not available for subscription`

Om din prenumeration behöver åtkomst i en viss region kan du använda alternativet **annan kvot förfrågan** för att begära åtkomst. I din begäran anger du det erbjudande och den SKU-information som du vill aktivera för regionen. Om du vill utforska erbjudande-och SKU-alternativen kan du läsa mer i [Azure Synapse Analytics-priser](https://azure.microsoft.com/pricing/details/synapse-analytics/).

![Annan kvot information](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>Skicka din begäran

Det sista steget är att fylla i återstående information om din SQL Database support förfrågan. Välj sedan **Nästa: Granska + skapa>>** . När du har gått igenom begärandeuppgifterna klickar du på **Skapa** för att skicka begäran.

## <a name="monitor-a-support-ticket"></a>Övervaka ett supportärende

När du har skickat in support förfrågan kontaktar support teamet för Azure. Du kan kontrollera status och detaljer för din begäran genom att klicka på **Alla supportärenden** på instrumentpanelen.

![Kontrollera status](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Andra resurser

Du kan också ansluta till Azure Synapse Analytics-communityn på [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) eller via webbplatsen [Microsoft Q&en fråga för Azure SQL Data Warehouse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).

