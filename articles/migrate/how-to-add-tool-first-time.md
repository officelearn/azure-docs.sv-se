---
title: Lägg till en utvärdering/Migreringsverktyget för första gången i Azure Migrate | Microsoft Docs
description: Beskriver hur du skapar ett Azure Migrate-projekt och lägger till en utvärdering/Migreringsverktyg.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812030"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Lägg till en utvärdering/Migreringsverktyget för första gången

Den här artikeln beskrivs hur du lägger till ett verktyg för utvärdering eller migrering till ett [Azure Migrate](migrate-overview.md) projekt för första gången.  
Azure Migrate ger en central knutpunkt för att spåra identifiering, bedömning och migrering av dina lokala appar och arbetsbelastningar och privata/offentliga virtuella datorer till Azure. Hubben innehåller Azure Migrate verktyg för bedömning och migrering, samt från tredje part, oberoende programvaruleverantör (ISV) [erbjudanden](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Skapa ett projekt och lägga till ett verktyg

Ställ in ett nytt Azure Migrate-projekt i en Azure-prenumeration och lägga till ett verktyg.

- Ett Azure Migrate-projekt används för att lagra identifiering, bedömning och migrering metadata som samlas in från miljön du bedöma eller migrerar. 
- I ett projekt kan du spåra identifierade tillgångar och dirigera bedömning och migrering.

1. I Azure portal > **alla tjänster**, Sök efter **Azure Migrate**.
2. Under **Services**väljer **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. I **översikt**, klickar du på **utvärdera och migrera servrar för**.
4. Under **Upptäck, utvärdera och migrera servrar för**, klickar du på **utvärdera och migrera servrar för**.

    ![Identifiera och utvärdera servrar](./media/how-to-add-tool-first-time/assess-migrate.png)

1. I **Upptäck, utvärdera och migrera servrar för**, klickar du på **lägga till verktyg**.
2. I **migrera projektet**, Välj din Azure-prenumeration och skapa en resursgrupp om du inte har något.
3. I **projektinformation**, ange projektnamn och geografisk plats där du vill skapa projektet. 

    ![Skapa ett Azure Migrate-projekt](./media/how-to-add-tool-first-time/migrate-project.png)

    Du kan skapa ett Azure Migrate-projekt i någon av dessa områden.

    **Geografi** | **Storage plats region**
    --- | ---
    Asien | Sydostasien eller Östasien
    Europa | Södra Europa eller Europa, västra
    Storbritannien | Storbritannien, södra eller Storbritannien, västra
    USA | USA, centrala eller USA, västra 2

    Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja en målregion för den faktiska migreringen.

4. Klicka på **nästa**, och Lägg till ett verktyg med bedömning och migrering.

    > [!NOTE]
    > Du måste lägga till minst en bedömning och migrering verktyget när du skapar ett projekt.

5. I **väljer utvärderingsvertyget**, lägga till ett verktyg för utvärdering. Om du inte behöver ett verktyg för utvärdering, Välj **hoppa över att lägga till en utvärderingsvertyget för tillfället** > **nästa**. 
2. I **väljer Migreringsverktyget**, lägga till ett migrationsverktyg efter behov. Om du inte behöver ett migrationsverktyg just nu, Välj **hoppa över att lägga till en Migreringsverktyget för tillfället** > **nästa**.
3. I **granska + Lägg till verktyg**, granskar du inställningarna och klickar på **lägga till verktyg**.

När du har skapat projektet kan du välja ytterligare verktyg för bedömning och migrering av servrar och arbetsbelastningar, databaser och webbappar.

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

I vissa fall kan du behöva skapa ytterligare Azure Migrate-projekt. Till exempel om du har datacenter i olika geografiska områden eller du behöver att lagra metadata i ett annat geografiskt område. Skapa ytterligare ett projekt på följande sätt:

1. I det aktuella Azure Migrate-projektet klickar du på **servrar** eller **databaser**.
2. I det övre högra hörnet, klickar du på **ändra** bredvid projektnamnet på aktuella.
3. I **inställningar**väljer **Klicka här för att skapa ett nytt projekt**.
4. Skapa ett nytt projekt och Lägg till ett nytt verktyg som beskrivs i föregående procedur.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till ytterligare [utvärdering](how-to-assess.md) och [migrering](how-to-migrate.md) verktyg. 
