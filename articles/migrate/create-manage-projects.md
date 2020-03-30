---
title: Skapa och hantera Azure Migrate-projekt
description: Hitta, skapa, hantera och ta bort projekt i Azure Migrate.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: a49595f0580e71048239d5c5d8f4d1a66e24fe6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269645"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Skapa och hantera Azure Migrate-projekt

I den här artikeln beskrivs hur du skapar, hanterar och tar bort [Azure Migrate-projekt.](migrate-services-overview.md)


## <a name="create-a-project-for-the-first-time"></a>Skapa ett projekt för första gången

Första gången du konfigurerar Azure Migrate skapar du ett projekt och lägger till ett utvärderings- eller migreringsverktyg. [Följ dessa instruktioner](how-to-add-tool-first-time.md) för att ställa in för första gången.

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Om du redan har ett Azure Migrate-projekt och vill skapa ytterligare ett projekt gör du följande:  

1. Sök efter Azure Migrate i [Azure-portalen](https://portal.azure.com). **Azure Migrate**
2. Välj **ändring** i det övre **Servers**högra hörnet på instrumentpanelen > För Azure Migrate.

   ![Ändra Azure Migrate-projekt](./media/create-manage-projects/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **klicka här**.

   ![Skapa ett andra Azure Migrate-projekt](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Hitta ett projekt

Hitta ett projekt enligt följande:

1. Sök efter Azure Migrate i [Azure-portalen](https://portal.azure.com). **Azure Migrate**
2. Välj **ändring** i det övre högra hörnet i instrumentpanelen för Azure Migrate > **servrar.**

    ![Växla till ett befintligt Azure Migrate-projekt](./media/create-manage-projects/switch-project.png)

3. Välj lämplig prenumeration och Azure Migrate-projekt.


Om du skapade projektet i den [tidigare versionen](migrate-services-overview.md#azure-migrate-versions) av Azure Migrate hittar du det på följande sätt:

1. Sök efter Azure Migrate i [Azure-portalen](https://portal.azure.com). **Azure Migrate**
2. Om du har skapat ett projekt i den tidigare versionen i instrumentpanelen För Azure Migrate visas en banner som refererar till äldre projekt. Välj banderollen.

    ![Få tillgång till befintliga projekt](./media/create-manage-projects/access-existing-projects.png)

3. Gå igenom listan över gamla projekt.


## <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort enligt följande:

1. Öppna azure-resursgruppen där projektet skapades.
2. På resursgruppssidan väljer du **Visa dolda typer**.
3. Markera det migrerande projekt som du vill ta bort och dess associerade resurser.
    - Resurstypen är **Microsoft.Migrate/migrateprojects**.
    - Om resursgruppen endast används av Azure Migrate-projektet kan du ta bort hela resursgruppen.


Tänk på följande:

- När du tar bort tas både projektet och metadata om identifierade datorer bort.
- Om du använder den äldre versionen av Azure Migrate öppnar du azure-resursgruppen där projektet skapades. Markera det migrerande projekt som du vill ta bort (resurstypen är **Migreringsprojekt**).
- Om du använder beroendeanalys med en Azure Log Analytics-arbetsyta:
    - Om du har anslutit en Log Analytics-arbetsyta till serverbedömningsverktyget tas arbetsytan inte bort automatiskt. Samma Log Analytics-arbetsyta kan användas för flera scenarier.
    - Om du vill ta bort arbetsytan Log Analytics gör du det manuellt.

### <a name="delete-a-workspace-manually"></a>Ta bort en arbetsyta manuellt

1. Bläddra till arbetsytan Log Analytics som är kopplad till projektet.

    - Om du inte har tagit bort Azure Migrate-projektet kan du hitta länken till arbetsytan i **Essentials** > **Server Assessment**.
       ![LA Arbetsyta](./media/create-manage-projects/loganalytics-workspace.png).
       
    - Om du redan har tagit bort Azure Migrate-projektet väljer du **Resursgrupper** i den vänstra rutan i Azure-portalen och hittar arbetsytan.
       
2. [Följ instruktionerna](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) för att ta bort arbetsytan.

## <a name="next-steps"></a>Nästa steg

Lägg till [bedömnings-](how-to-assess.md) eller [migreringsverktyg](how-to-migrate.md) i Azure Migrate-projekt.
