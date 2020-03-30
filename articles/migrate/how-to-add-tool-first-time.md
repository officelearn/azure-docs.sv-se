---
title: Lägga till ett bedömnings-/migreringsverktyg i Azure Migrate
description: Beskriver hur du skapar ett Azure Migrate-projekt och lägger till ett utvärderings-/migreringsverktyg.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185937"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Lägga till ett utvärderings-/migreringsverktyg för första gången

I den här artikeln beskrivs hur du lägger till ett utvärderings- eller migreringsverktyg i ett [Azure Migrate-projekt](migrate-overview.md) för första gången.  
Azure Migrate är en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer i molnet till Azure. Hubben tillhandahåller Azure Migrate-verktyg för utvärdering och migrering, samt andra verktyg och oberoende [isv-erbjudanden](migrate-services-overview.md#isv-integration) (Software Vendor). 

## <a name="create-a-project-and-add-a-tool"></a>Skapa ett projekt och lägga till ett verktyg

Konfigurera ett nytt Azure Migrate-projekt i en Azure-prenumeration och lägg till ett verktyg.

- Ett Azure Migrate-projekt används för att lagra identifierings-, utvärderings- och migreringsmetadata som samlats in från den miljö som du utvärderar eller migrerar. 
- I ett projekt kan du spåra identifierade tillgångar och dirigera bedömning och migrering.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **Upptäck, utvärdera och migrera servrar**klickar du på Utvärdera och migrera **servrar**.

    ![Upptäck och utvärdera servrar](./media/how-to-add-tool-first-time/assess-migrate.png)

1. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
2. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
3. Ange projektnamnet och geografin där du vill skapa projektet i **Projektinformation.** 

    ![Skapa ett Azure Migrate-projekt](./media/how-to-add-tool-first-time/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa geografiska områden.

   **Geografi** | **Lagerplatsregion**
    --- | ---
    Asien   | Sydostasien eller Östasien
    Europa | Europa, norra eller Europa, västra
    Japan  | Japan Öst eller Västra Japan
    Storbritannien | Storbritannien Syd- eller Storbritannien Väst
    USA | Centrala USA eller västra USA 2
    Kanada | Kanada, centrala
    Indien  | Indien Central eller Indien South
    Australien | Australien i söder

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja ett målområde för den faktiska migreringen.

    Om du vill ange en specifik region inom en geografi för distribution av migrera projektet och dess associerade resurser (Principbegränsningar i din prenumeration kan tillåta distribution av Azure-resurser endast till en viss Azure-region), kan du använda nedanstående API för att skapa ett migrerande projekt. Ange prenumerations-ID, resursgruppnamn, Migrera projektnamn tillsammans med plats(någon av de Azure-regioner som nämns i tabellen där Azure Migrate distribueras.)

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klicka på **Nästa**och lägg till ett bedömnings- eller migreringsverktyg.

    > [!NOTE]
    > När du skapar ett projekt måste du lägga till minst ett bedömnings- eller migreringsverktyg.

5. Lägg till ett bedömningsverktyg i **välj bedömningsverktyg.** Om du inte behöver ett bedömningsverktyg väljer du **Hoppa över att lägga till ett bedömningsverktyg för nu** > **Nästa**. 
2. Lägg till ett migreringsverktyg i **Välj migreringsverktyg**efter behov. Om du inte behöver ett migreringsverktyg just nu väljer du **Hoppa över att lägga till ett migreringsverktyg för nu** > **Nästa**.
3. Granska inställningarna i **Verktyg för Granskning + lägg till**och klicka på Lägg till **verktyg**.

När du har skapat projektet kan du välja ytterligare verktyg för bedömning och migrering av servrar och arbetsbelastningar, databaser och webbappar.

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

I vissa fall kan du behöva skapa ytterligare Azure Migrate-projekt. Till exempel om du har datacenter i olika geografiska områden, eller om du behöver lagra metadata i en annan geografi. Skapa ytterligare ett projekt på följande sätt:

1. Klicka på **Servrar** eller **databaser**i det aktuella Azure Migrate-projektet .
2. Klicka på **Ändra bredvid** det aktuella projektnamnet i det övre högra hörnet.
3. I **Inställningar**väljer du **Klicka här för att skapa ett nytt projekt**.
4. Skapa ett nytt projekt och lägg till ett nytt verktyg enligt beskrivningen i föregående procedur.

## <a name="next-steps"></a>Nästa steg

Läs om hur du lägger till ytterligare [bedömnings-](how-to-assess.md) och [migreringsverktyg.](how-to-migrate.md) 
