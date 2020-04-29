---
title: Lägg till ett verktyg för bedömning/migrering i Azure Migrate
description: Beskriver hur du skapar ett Azure Migrate-projekt och lägger till ett verktyg för bedömning/migrering.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 48bdea31d17ea1ddf0b983af962dce30b22d8dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537737"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Lägga till ett utvärderings-/migreringsverktyg för första gången

Den här artikeln beskriver hur du lägger till ett utvärderings-eller Migreringsverktyg till ett [Azure Migrate](migrate-overview.md) projekt för första gången.  
Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av dina lokala appar och arbets belastningar och privata/offentliga virtuella moln datorer till Azure. Hubben innehåller Azure Migrate verktyg för utvärdering och migrering, samt andra verktyg och oberoende program varu leverantörer (ISV)- [erbjudanden](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Skapa ett projekt och lägga till ett verktyg

Skapa ett nytt Azure Migrate-projekt i en Azure-prenumeration och Lägg till ett verktyg.

- Ett Azure Migrate-projekt används för att lagra identifierings-, utvärderings-och migrerings-metadata som samlas in från den miljö som du bedömer eller migrerar. 
- I ett projekt kan du spåra identifierade till gångar och dirigera utvärdering och migrering.

1. I Azure-portalen > **Alla tjänster** söker du efter **Azure Migrate**.
2. Under **Tjänster** väljer du **Azure Migrate**.

    ![Konfigurera Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. I **översikten** klickar du på **Utvärdera och migrera servrar**.
4. Under **identifiera, utvärdera och migrera servrar**klickar du på **utvärdera och migrera servrar**.

    ![Identifiera och utvärdera servrar](./media/how-to-add-tool-first-time/assess-migrate.png)

1. I **Discover, assess and migrate servers** (Identifiera, utvärdera och migrera servrar) klickar du på **Lägg till verktyg**.
2. I **Migrera projekt** väljer du din Azure-prenumeration och skapar en resursgrupp om du inte har någon.
3. I **projekt information**anger du det projekt namn och geografi som du vill skapa projektet i.  Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Skapa ett Azure Migrate-projekt](./media/how-to-add-tool-first-time/migrate-project.png)

    - Den angivna geografiska platsen för projektet används bara för att lagra de metadata som samlats in från lokala virtuella datorer. Du kan välja valfri mål region för den faktiska migreringen.
    - Om du behöver distribuera ett projekt inom en angiven region i en geografi använder du följande API för att skapa ett projekt. Ange prenumerations-ID, resurs grupp namn och projekt namn, tillsammans med platsen. Granska geografiska områden/regioner för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Klicka på **Nästa**och Lägg till ett verktyg för bedömning eller migrering.

    > [!NOTE]
    > När du skapar ett projekt måste du lägga till minst ett verktyg för bedömning eller migrering.

5. I **Välj bedömnings verktyg**lägger du till ett utvärderings verktyg. Om du inte behöver ett bedömnings verktyg väljer du **hoppa över Lägg till ett bedömnings verktyg för** > **Nästa**gång. 
2. I **Välj Migreringsverktyg**lägger du till ett Migreringsverktyg efter behov. Om du inte behöver ett Migreringsverktyg just nu väljer du **hoppa över Lägg till ett Migreringsverktyg för** > **Nästa**gång.
3. I **Granska + Lägg till verktyg**granskar du inställningarna och klickar på **Lägg till verktyg**.

När du har skapat projektet kan du välja ytterligare verktyg för utvärdering och migrering av servrar och arbets belastningar, databaser och webbappar.

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

I vissa fall kan du behöva skapa ytterligare Azure Migrate projekt. Om du till exempel har data Center i olika geografiska områden eller om du behöver lagra metadata i en annan geografi. Skapa ett ytterligare projekt enligt följande:

1. I det aktuella Azure Migrate-projektet klickar du på **servrar** eller **databaser**.
2. Klicka på **ändra** bredvid det aktuella projekt namnet i det övre högra hörnet.
3. I **Inställningar**väljer **du klicka här för att skapa ett nytt projekt**.
4. Skapa ett nytt projekt och Lägg till ett nytt verktyg enligt beskrivningen i föregående procedur.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du lägger till ytterligare verktyg för [bedömning](how-to-assess.md) och [migrering](how-to-migrate.md) . 
