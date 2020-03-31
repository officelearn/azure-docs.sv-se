---
title: Distribuera resurser med Azure-portal
description: Använd Azure-portalen och Azure Resource Manage för att distribuera dina resurser till en resursgrupp i din prenumeration.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: ea2faf810b3a5d1b8fa46575201022a501cc7d58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153445"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>Distribuera resurser med ARM-mallar och Azure-portal

Lär dig hur du använder [Azure-portalen](https://portal.azure.com) med [ARM-mallar (Azure Resource Manager)](overview.md) för att distribuera dina Azure-resurser. Mer information om hur du hanterar dina resurser finns i [Hantera Azure-resurser med hjälp av Azure-portalen](../management/manage-resources-portal.md).

Distribuera Azure-resurser med hjälp av Azure-portalen innebär vanligtvis två steg:

- Skapa en resursgrupp.
- Distribuera resurser till resursgruppen.

Dessutom kan du distribuera en ARM-mall för att skapa Azure-resurser.

Den här artikeln visar båda metoderna.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

1. Om du vill skapa en ny resursgrupp väljer du **Resursgrupper** från [Azure-portalen](https://portal.azure.com).

   ![Välj resursgrupper](./media/deploy-portal/select-resource-groups.png)

1. Under Resursgrupper väljer du **Lägg till**.

   ![Lägga till resursgrupp](./media/deploy-portal/add-resource-group.png)

1. Markera eller ange följande egenskapsvärden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resursgrupp**: Ge resursgruppen ett namn.
    - **Region**: Ange en Azure-plats. Det är här resursgruppen lagrar metadata om resurserna. Av efterlevnadsskäl kanske du vill ange var metadata lagras. I allmänhet rekommenderar vi att du anger en plats där de flesta av dina resurser ska finnas. Om du använder samma plats kan mallen förenklas.

   ![Ange gruppvärden](./media/deploy-portal/set-group-properties.png)

1. Välj **Granska + skapa**.
1. granska värdena och välj sedan **Skapa**.
1. Välj **Uppdatera** innan du kan se den nya resursgruppen i listan.

## <a name="deploy-resources-to-a-resource-group"></a>Distribuera resurser till en resursgrupp

När du har skapat en resursgrupp kan du distribuera resurser till gruppen från Marketplace. Marketplace tillhandahåller fördefinierade lösningar för vanliga scenarier.

1. Om du vill starta en distribution väljer du **Skapa en resurs** från [Azure-portalen](https://portal.azure.com).

   ![Ny resurs](./media/deploy-portal/new-resources.png)

1. Hitta den typ av resurs som du vill distribuera. Resurserna är ordnade i kategorier. Om du inte ser den specifika lösningen som du vill distribuera kan du söka på Marketplace efter den. Följande skärmbild visar att Ubuntu Server är markerat.

   ![Välj resurstyp](./media/deploy-portal/select-resource-type.png)

1. Beroende på vilken typ av vald resurs som ska väljas har du en samling relevanta egenskaper som du kan ange före distributionen. För alla typer måste du välja en målresursgrupp. Följande avbildning visar hur du skapar en virtuell Linux-dator och distribuerar den till den resursgrupp som du skapade.

   ![Skapa resursgrupp](./media/deploy-portal/select-existing-group.png)

   Du kan också välja att skapa en resursgrupp när du distribuerar resurserna. Välj **Skapa nytt** och ge resursgruppen ett namn.

1. Distributionen börjar. Distributionen kan ta flera minuter. Vissa resurser tar längre tid än andra resurser. När distributionen är klar visas ett meddelande. Välj **Gå till resurs** för att öppna

   ![Visa meddelande](./media/deploy-portal/view-notification.png)

1. När du har distribuerat resurserna kan du lägga till fler resurser i resursgruppen genom att välja **Lägg till**.

   ![Lägg till resurs](./media/deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Distribuera resurser från anpassad mall

Om du vill köra en distribution men inte använda någon av mallarna på Marketplace kan du skapa en anpassad mall som definierar infrastrukturen för din lösning. Mer information om hur du skapar mallar finns [i Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).

> [!NOTE]
> Portalgränssnittet stöder inte refererar till en [hemlighet från ett Key Vault](key-vault-parameter.md). Använd i stället [PowerShell](deploy-powershell.md) eller [Azure CLI](deploy-cli.md) för att distribuera mallen lokalt eller från en extern URI.

1. Om du vill distribuera en anpassad mall via portalen väljer du **Skapa en resurs**, söker efter **mall**. och välj sedan **Malldistribution**.

   ![Distribution av sökmall](./media/deploy-portal/search-template.png)

1. Välj **Skapa**.
1. Du ser flera alternativ för att skapa en mall:

    - **Skapa din egen mall i redigeraren:** skapa en mall med hjälp av portalmallredigeraren.  Redigeraren kan lägga till ett resursmallschema.
    - **Vanliga mallar:** Det finns fyra vanliga mallar för att skapa en virtuell Linux-dator, virtuell Windows-dator, ett webbprogram och en Azure SQL-databas.
    - **Läs in en GitHub-snabbstartsmall:** använd en befintlig [snabbstartsmallar](https://azure.microsoft.com/resources/templates/).

   ![Visa alternativ](./media/deploy-portal/see-options.png)

    Den här självstudien innehåller instruktioner för hur du läser in en snabbstartsmall.

1. Skriv eller välj **101-storage-account-create**under **Läs in en snabbstartsmall för GitHub**.

    Du kan välja mellan två alternativ:

    - **Välj mall**: distribuera mallen.
    - **Redigera mall:** Redigera snabbstartsmallen innan du distribuerar den.

1. Välj **Redigera mall** om du vill utforska redigeraren för portalmallar. Mallen läses in i redigeraren. Observera att det finns två parametrar: **storageAccountType** och **plats**.

   ![Skapa mallen](./media/deploy-portal/show-json.png)

1. Gör en mindre ändring av mallen. Uppdatera till exempel variabeln **storageAccountName** till:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Välj **Spara**. Nu ser du distributionsgränssnittet för portalmallen. Lägg märke till de två parametrar som du har definierat i mallen.
1. Ange eller välj egenskapsvärden:

    - **Prenumeration**: Välj en Azure-prenumeration.
    - **Resursgrupp**: Välj **Skapa nytt** och ge ett namn.
    - **Plats**: Välj en Azure-plats.
    - **Typ av lagringskonto**: Använd standardvärdet.
    - **Plats**: Använd standardvärdet.
    - **Jag godkänner villkoren ovan**: (välj)

1. Välj **Köp**.

## <a name="next-steps"></a>Nästa steg

- Om du vill visa granskningsloggar finns [i Granskningsåtgärder med Resurshanteraren](../management/view-activity-logs.md).
- Läser Visa [distributionsåtgärder](deployment-history.md).
- Information om hur du exporterar en mall från en distributions- eller resursgrupp finns i [Exportera ARM-mallar](export-template-portal.md).
- Information om hur du distribuerar tjänsten på ett säkert sätt i flera regioner finns i [Azure Deployment Manager](deployment-manager-overview.md).
