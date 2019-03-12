---
title: Gör webb- och API-appar tillgängliga för din Azure Stack-användare | Microsoft Docs
description: Självstudie för att installera App Service-resursprovider och skapa erbjuder möjligheten att skapa webb- och API-appar som ger Azure Stack-användare.
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
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f09ed70647eaa0275020bdac66e0fcf84d2ff00a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760383"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Självstudie: gör webb- och API-appar tillgängliga för Azure Stack-användare

Som administratör för Azure Stack-molnet kan du skapa erbjudanden som användarna kan skapa Azure Functions- och webb-och API för (klienter). Genom att ge användarna åtkomst till apparna på begäran, molnbaserade, kan du spara dem tid och resurser.

Om du vill ställa in, kommer du att:

> [!div class="checklist"]
> * Distribuera App Service-resursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

## <a name="deploy-the-app-service-resource-provider"></a>Distribuera App Service-resursprovider

1. [Förbereda Azure Stack Development Kit värden](azure-stack-app-service-before-you-get-started.md). Detta inkluderar distribuera SQL Server-resursprovidern som krävs för att skapa vissa appar.
2. [Ladda ned installationsprogrammet och helper skripten](azure-stack-app-service-deploy.md).
3. [Kör helper-skript för att skapa nödvändiga certifikat](azure-stack-app-service-deploy.md).
4. [Installera App Service-resursprovider](azure-stack-app-service-deploy.md) (det kan ta ett par timmar att installera och för alla worker-roller ska visas.)
5. [Verifiera installationen](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Skapa ett erbjudande

Exempelvis kan du skapa ett erbjudande som låter användarna skapa DNN web content management system. Det kräver SQL Server-tjänsten som du redan har aktiverat genom att installera SQL Server-resursprovidern.

1.  [Anger en kvot för](azure-stack-setting-quotas.md) och ge den namnet *AppServiceQuota*. Välj **Microsoft.Web** för den **Namespace** fält.
2.  [Skapa en plan](azure-stack-create-plan.md). Ge den namnet *TestAppServicePlan*väljer den **Microsoft.SQL** service och **AppService kvot** kvot.

    > [!NOTE]
    > Om du vill låta användarna skapa andra appar, kan andra tjänster krävas i planen. Till exempel Azure Functions kräver den **Microsoft.Storage** tjänsten i planen, medan Wordpress kräver **Microsoft.MySQL**.

3.  [Skapa ett erbjudande](azure-stack-create-offer.md), ge den namnet **TestAppServiceOffer** och välj den **TestAppServicePlan** plan.

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har distribuerat App Service-resursprovider och skapat ett erbjudande kan du logga in som en användare prenumererar på erbjudandet och skapa en app.

I det här exemplet ska vi skapa en DNN Platform innehållshanteringssystem. Först skapar du en SQL-databas och sedan DNN-webbappen.

### <a name="subscribe-to-the-offer"></a>Prenumerera på erbjudandet

1. Logga in på Azure Stack-portalen (https://portal.local.azurestack.external) som en klient.
2. Välj **skaffa en prenumeration** >, ange **TestAppServiceSubscription** under **visningsnamn** > **Välj ett erbjudande**  >  **TestAppServiceOffer** > **skapa**.

### <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Välj **+**  >  **Data + lagring** > **SQL-databas**.
2. Behåll standardvärdena, förutom för följande fält:

    - **Databasnamn**: DNNdb
    - **Maximal storlek i MB**: 100
    - **Prenumeration**: TestAppServiceOffer
    - **Resursgrupp**: DNN-RG

3. Välj **inloggningsinställningar**, anger du autentiseringsuppgifterna för databasen och välj sedan **OK**. Senare i den här självstudien ska du använda de här autentiseringsuppgifterna.
4. Under **SKU** > Välj den SQL-SKU som du skapade för SQL Server som är värd för > och välj sedan **OK**.
5. Välj **Skapa**.

### <a name="create-a-dnn-app"></a>Skapa en DNN-app

1. Välj **+**  >  **se alla** > **DNN Platform förhandsversion** > **skapa** .
2. Ange *DNNapp* under **appnamn** och välj **TestAppServiceOffer** under **prenumeration**.
3. Välj **konfigurera nödvändiga inställningar** > **Skapa ny** > Ange en **App Service-plan** namn.
4. Välj **prisnivå** > **F1 kostnadsfri** > **Välj** > **OK**.
5. Välj **databasen** och ange autentiseringsuppgifter för SQL-databasen som du skapade tidigare.
6. Välj **Skapa**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera App Service-resursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

Fortsätt till nästa självstudie och lär dig hur du:

> [!div class="nextstepaction"]
> [Distribuera appar till Azure och Azure Stack](user/azure-stack-solution-pipeline.md)
