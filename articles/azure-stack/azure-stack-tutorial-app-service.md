---
title: "Göra webb- och API-appar tillgängliga för användarna Azure Stack | Microsoft Docs"
description: "Kursen hjälper dig att installera App-tjänstresursprovider och skapa erbjuder som ger Azure Stack användarna möjlighet att skapa webb- och API apps."
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
ms.openlocfilehash: 5978706f2cab69c83a49bfd0e15ae904a38c7bab
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Göra webb- och API apps tillgängliga för användarna i Azure-stacken

Du kan skapa erbjudanden som användarna kan som administratör Azure Stack-moln (klienter) skapa Azure Functions- och webb- och API-program. Genom att ge tillgång till apparna på begäran, molnbaserad till användarna, kan du spara dem tid och resurser. Om du vill ställa in, kommer du att:

> [!div class="checklist"]
> * Distribuera App-tjänstresursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

## <a name="deploy-the-app-service-resource-provider"></a>Distribuera App-tjänstresursprovider

1. [Förbereda värden Azure Stack Development Kit](azure-stack-app-service-before-you-get-started.md). Detta innefattar distribution av SQL Server-resursprovidern som krävs för att skapa vissa appar.
2. [Ladda ned installationsprogrammet och helper skripten](azure-stack-app-service-deploy.md).
3. [Kör skriptet helper för att skapa nödvändiga certifikat](azure-stack-app-service-deploy.md).
4. [Installera App-tjänstresursprovider](azure-stack-app-service-deploy.md) (tar några timmar att installera och för alla arbetsroller visas).
5. [Verifiera installationen](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Skapa ett erbjudande

Du kan skapa ett erbjudande som låter användarna skapa DNN web innehållshanteringssystem som ett exempel. Den kräver SQL Server-tjänsten som du redan har aktiverat genom att installera SQL Server-resursprovidern.

1.  [Anger en kvot](azure-stack-setting-quotas.md) och ger den namnet *AppServiceQuota*. Välj **Microsoft.Web** för den **Namespace** fältet.
2.  [Skapa en plan](azure-stack-create-plan.md). Ge den namnet *TestAppServicePlan*, Välj den **Microsoft.SQL** tjänsten, och **Apptjänst kvot** kvoten.

    > [!NOTE]
    > Andra tjänster kan krävas i planen för att låta användarna skapa andra appar. Till exempel Azure Functions kräver att planen omfattar den **Microsoft.Storage** service, medan Wordpress kräver **Microsoft.MySQL**.
    > 
    >

3.  [Skapa ett erbjudande](azure-stack-create-offer.md), ge den namnet **TestAppServiceOffer** och välj den **TestAppServicePlan** plan.

## <a name="test-the-offer"></a>Testa erbjudandet

Nu när du har distribuerat appen tjänstresursprovider och skapat ett erbjudande, du kan logga in som en användare kan prenumerera på erbjudandet och skapa en app. I det här exemplet skapar vi ett DNN plattform-system. Du måste först skapa en SQL-databas och DNN webbprogram.

### <a name="subscribe-to-the-offer"></a>Prenumerera på erbjudandet
1. Logga in på Azure Stack-portal (https://portal.local.azurestack.external) som en klient.
2. Klicka på **skaffa en prenumeration** > typen **TestAppServiceSubscription** under **visningsnamn** > **Välj ett erbjudande**  >  **TestAppServiceOffer** > **skapa**.

### <a name="create-a-sql-database"></a>Skapa en SQL-databas

1. Klicka på  **+**   >  **Data + lagring** > **SQL-databas**.
2. Lämnar du standardinställningarna för fält, utom på följande sätt:
    - **Databasnamn**: DNNdb
    - **Max storlek i MB**: 100
    - **Prenumerationen**: TestAppServiceOffer
    - **Resursgruppen**: DNN RG
3. Klicka på **inloggningsinställningar**ange autentiseringsuppgifter för databasen och klicka sedan på **OK**. Senare i den här övningen ska du använda de här autentiseringsuppgifterna.
4. Klicka på **SKU** > Välj SQL-SKU som du skapade för värd för SQLServer > **OK**.
5. Klicka på **Skapa**.

### <a name="create-a-dnn-app"></a>Skapa en DNN-app    

1. Klicka på  **+**   >  **se alla** > **DNN plattform preview** > **skapa**.
2. Typen *DNNapp* under **appnamn** och välj **TestAppServiceOffer** under **prenumeration**.
3. Klicka på **konfigurera nödvändiga inställningar** > **Skapa nytt** > typen en **programtjänstplanen** namn.
4. Klicka på **prisnivå** > **F1 kostnadsfri** > **Välj** > **OK**.
5. Klicka på **databasen** och ange information om SQL-databasen som du skapade tidigare.
6. Klicka på **Skapa**.

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera App-tjänstresursprovider
> * Skapa ett erbjudande
> * Testa erbjudandet

Gå till nästa kurs att lära dig hur du:

> [!div class="nextstepaction"]
> [Distribuera appar till Azure och Azure Stack](user/azure-stack-solution-pipeline.md)
