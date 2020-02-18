---
title: Skapa och hantera Azure Migrate-projekt
description: Hitta, skapa, hantera och ta bort projekt i Azure Migrate.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425671"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Skapa och hantera Azure Migrate-projekt

Den här artikeln beskriver hur du skapar, hanterar och tar bort [Azure Migrate](migrate-services-overview.md) projekt.


## <a name="create-a-project-for-the-first-time"></a>Skapa ett projekt för första gången

Första gången du ställer in Azure Migrate skapar du ett projekt och lägger till ett verktyg för bedömning eller migrering. [Följ de här anvisningarna](how-to-add-tool-first-time.md) för att konfigurera för första gången.

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Om du redan har ett Azure Migrate projekt och vill skapa ett nytt projekt gör du följande:  

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. På Azure Migrate instrument panelens >- **servrar**väljer du **ändra** i det övre högra hörnet.

   ![Ändra Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **Klicka här**.

   ![Skapa ett andra Azure Migrate-projekt](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Hitta ett projekt

Sök efter ett projekt enligt följande:

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. I Azure Migrate instrument panelens >- **servrar**väljer du **ändra** i det övre högra hörnet.

    ![Växla till ett befintligt Azure Migrate-projekt](./media/create-manage-projects/switch-project.png)

3. Välj lämplig prenumeration och Azure Migrate projekt.


Om du har skapat projektet i den [tidigare versionen](migrate-services-overview.md#azure-migrate-versions) av Azure Migrate kan du söka efter det på följande sätt:

1. Sök efter **Azure Migrate**i [Azure Portal](https://portal.azure.com).
2. Om du har skapat ett projekt i den tidigare versionen av Azure Migrate instrument panelen visas en banderoll som refererar till äldre projekt. Välj banderollen.

    ![Komma åt befintliga projekt](./media/create-manage-projects/access-existing-projects.png)

3. Granska listan över gamla projekt.


## <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort enligt följande:

1. Öppna Azure-resurs gruppen där projektet skapades.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Välj det migrerade projektet som du vill ta bort och dess associerade resurser.
    - Resurs typen är **Microsoft. Migrate/migrateprojects**.
    - Om resurs gruppen uteslutande används av Azure Migrate-projektet kan du ta bort hela resurs gruppen.


Tänk på följande:

- När du tar bort raderas både projektet och metadata om identifierade datorer.
- Om du använder den äldre versionen av Azure Migrate öppnar du Azure-resurs gruppen där projektet skapades. Välj det migrerings projekt som du vill ta bort (resurs typen är **migreringsjobb**).
- Om du använder beroende analys med en Azure Log Analytics-arbetsyta:
    - Om du har kopplat en Log Analytics arbets yta till Server Assessment-verktyget tas arbets ytan bort automatiskt. Samma Log Analytics arbets yta kan användas för flera scenarier.
    - Om du vill ta bort arbets ytan Log Analytics gör du det manuellt.

### <a name="delete-a-workspace-manually"></a>Ta bort en arbets yta manuellt

1. Bläddra till Log Analytics arbets ytan som är kopplad till projektet.

    - Om du inte har tagit bort Azure Migrate-projektet hittar du en länk till arbets ytan i **grunderna** > **Server bedömning**.
       ![LA-arbetsytan](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Om du redan har tagit bort Azure Migrate-projektet väljer du **resurs grupper** i den vänstra rutan i Azure Portal och letar upp arbets ytan.
       
2. [Följ anvisningarna](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) för att ta bort arbets ytan.

## <a name="next-steps"></a>Nästa steg

Lägg till [utvärderings](how-to-assess.md) -eller [migreringsverktyg](how-to-migrate.md) för att Azure Migrate projekt.
