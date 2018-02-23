---
title: "Tillgängliggöra SQL-databaser till Azure-stacken användarna | Microsoft Docs"
description: "Kursen hjälper dig att installera SQL Server-resursprovidern och skapa erbjuder som gör att Azure-stacken användarna skapa SQL-databaser."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: e9fd74fa44bb9482ee2285f4305085ee6ff2fb73
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Tillgängliggöra SQL-databaser till Azure Stack-användare

Du kan skapa erbjudanden som användarna kan som administratör Azure Stack-moln (klienter) skapa SQL-databaser som de kan använda med sina moln-ursprungliga appar, webbplatser och arbetsbelastningar. Genom att tillhandahålla dessa anpassade på begäran, molnbaserade databaser till dina användare kan spara du dem tid och resurser. Om du vill ställa in, kommer du att:

> [!div class="checklist"]
> * Distribuera SQL Server-resursprovidern
> * Skapa ett erbjudande
> * Testa erbjudandet

## <a name="deploy-the-sql-server-resource-provider"></a>Distribuera SQL Server-resursprovidern

Distributionsprocessen beskrivs i detalj i den [Använd SQL-databaser på Azure-stacken artikel](azure-stack-sql-resource-provider-deploy.md), som består av följande primära steg:

1. [Distribuera SQL-resursprovidern]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider).
2. [Kontrollera distributionen]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Ange kapacitet genom att ansluta till en SQL-server som värd.

## <a name="create-an-offer"></a>Skapa ett erbjudande

1.  [Anger en kvot](azure-stack-setting-quotas.md) och ger den namnet *SQLServerQuota*. Välj **Microsoft.SQLAdapter** för den **Namespace** fältet.
2.  [Skapa en plan](azure-stack-create-plan.md). Ge den namnet *TestSQLServerPlan*, Välj den **Microsoft.SQLAdapter** tjänsten, och **SQLServerQuota** kvoten.

    > [!NOTE]
    > Andra tjänster kan krävas i planen för att låta användarna skapa andra appar. Till exempel Azure Functions kräver att planen omfattar den **Microsoft.Storage** service, medan Wordpress kräver **Microsoft.MySQLAdapter**.
    > 
    >

3.  [Skapa ett erbjudande](azure-stack-create-offer.md), ge den namnet **TestSQLServerOffer** och välj den **TestSQLServerPlan** plan.

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har distribuerat resursprovidern SQL Server och skapat ett erbjudande, du kan logga in som en användare kan prenumerera på erbjudandet och skapa en databas.

### <a name="subscribe-to-the-offer"></a>Prenumerera på erbjudandet
1. Logga in på Azure Stack-portal (https://portal.local.azurestack.external) som en klient.
2. Klicka på **skaffa en prenumeration** och skriv sedan **TestSQLServerSubscription** under **visningsnamn**.
3. Klicka på **Välj ett erbjudande** > **TestSQLServerOffer** > **skapa**.
4. Klicka på **fler tjänster** > **prenumerationer** > **TestSQLServerSubscription** > **resurs providers**.
5. Klicka på **registrera** bredvid den **Microsoft.SQLAdapter** provider.

### <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Klicka på  **+**   >  **Data + lagring** > **SQL-databas**.
2. Lämna standardvärdena för fält eller så kan du använda de här exemplen:
    - **Databasnamn**: SQLdb
    - **Max storlek i MB**: 100
    - **Prenumerationen**: TestSQLOffer
    - **Resursgruppen**: SQL-RG
3. Klicka på **inloggningsinställningar**ange autentiseringsuppgifter för databasen och klicka sedan på **OK**.
4. Klicka på **SKU** > Välj SQL-SKU som du skapade för värd för SQLServer > **OK**.
5. Klicka på **Skapa**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera SQL Server-resursprovidern
> * Skapa ett erbjudande
> * Testa erbjudandet

Gå till nästa kurs att lära dig hur du:

> [!div class="nextstepaction"]
> [Göra webb-, mobil- och API-appar tillgängliga för användarna]( azure-stack-tutorial-app-service.md)

