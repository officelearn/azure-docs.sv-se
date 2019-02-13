---
title: Tillgängliggöra SQL-databaser till Azure Stack-användare | Microsoft Docs
description: Självstudie för att installera SQL Server-resursprovidern och skapa erbjuder som gör att Azure Stack-användare skapa SQL-databaser.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: aba9014e74b8ba0cd1b528cf570d3651950c6d5f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56207066"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Självstudie: tillgängliggöra SQL-databaser till Azure Stack-användare

Som administratör för Azure Stack-molnet kan du skapa erbjudanden som användarna kan (klienter) skapa SQL-databaser som de kan använda med deras molnbaserade appar, webbplatser och arbetsbelastningar. Genom att tillhandahålla dessa anpassade på begäran, molnbaserade databaser till dina användare, kan du spara dem tid och resurser. Om du vill ställa in, kommer du att:

> [!div class="checklist"]
> * Distribuera SQL Server-resursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

## <a name="deploy-the-sql-server-resource-provider"></a>Distribuera SQL Server-resursprovider

Distributionsprocessen beskrivs i detalj i de [använder SQL-databaser på Azure Stack-artikeln](azure-stack-sql-resource-provider-deploy.md), och består av följande huvudsakliga steg:

1. [Distribuera SQL-resursprovider](azure-stack-sql-resource-provider-deploy.md).
2. [Kontrollera distributionen](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Ange kapacitet genom att ansluta till en SQL-server som värd. Mer information finns i [Lägg till som är värd för servrar](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Skapa ett erbjudande

1.  [Anger en kvot för](azure-stack-setting-quotas.md) och ge den namnet *SQLServerQuota*. Välj **Microsoft.SQLAdapter** för den **Namespace** fält.
2.  [Skapa en plan](azure-stack-create-plan.md). Ge den namnet *TestSQLServerPlan*väljer den **Microsoft.SQLAdapter** -tjänsten och **SQLServerQuota** kvot.

    > [!NOTE]
    > Om du vill låta användarna skapa andra appar, kan andra tjänster krävas i planen. Till exempel Azure Functions kräver den **Microsoft.Storage** tjänsten i planen, medan Wordpress kräver **Microsoft.MySQLAdapter**.

3.  [Skapa ett erbjudande](azure-stack-create-offer.md), ge den namnet **TestSQLServerOffer** och välj den **TestSQLServerPlan** plan.

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har distribuerat den SQL Server-resursprovidern och skapat ett erbjudande kan du logga in som en användare prenumererar på erbjudandet och skapa en databas.

### <a name="subscribe-to-the-offer"></a>Prenumerera på erbjudandet

1. Logga in på Azure Stack-portalen (https://portal.local.azurestack.external) som en klient.
2. Välj **skaffa en prenumeration** och ange sedan **TestSQLServerSubscription** under **visningsnamn**.
3. Välj **Välj ett erbjudande** > **TestSQLServerOffer** > **skapa**.
4. Välj **alla tjänster** > **prenumerationer** > **TestSQLServerSubscription** > **resurs providers**.
5. Välj **registrera** bredvid den **Microsoft.SQLAdapter** provider.

### <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Välj **+**  >  **Data + lagring** > **SQL-databas**.
2. Behåll standardvärdena eller använda de här exemplen för följande fält:
    - **Databasnamn**: SQLdb
    - **Maximal storlek i MB**: 100
    - **Prenumeration**: TestSQLOffer
    - **Resursgrupp**: SQL-RG
3. Välj **inloggningsinställningar**, anger du autentiseringsuppgifterna för databasen och välj sedan **OK**.
4. Välj **SKU** > Välj den SQL-SKU som du skapade för SQL Server som är värd för > och välj sedan **OK**.
5. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera SQL Server-resursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

Fortsätt till nästa självstudie och lär dig hur du:

> [!div class="nextstepaction"]
> [Gör webb, mobil- och API-appar tillgängliga för dina användare]( azure-stack-tutorial-app-service.md)