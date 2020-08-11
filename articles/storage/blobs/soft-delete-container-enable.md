---
title: Aktivera och hantera mjuk borttagning för behållare (förhands granskning)
titleSuffix: Azure Storage
description: Aktivera mjuk borttagning av behållare (för hands version) för att lättare återställa data när de ändras felaktigt eller tas bort.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: f79b198b512547b1d93fbbd458af8251b109e62f
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88066669"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Aktivera och hantera mjuk borttagning för behållare (förhands granskning)

Container mjuk borttagning (för hands version) skyddar dina data från att oavsiktligt eller felaktigt ändras eller tas bort. Om mjuk borttagning av behållare är aktiverat för ett lagrings konto kan en behållare och dess innehåll återställas när det har tagits bort, inom en kvarhållningsperiod som du anger.

Om det finns en risk att dina data av misstag kan ändras eller tas bort av ett program eller en annan lagrings konto användare rekommenderar Microsoft att du aktiverar mjuk borttagning av behållare. Den här artikeln visar hur du aktiverar mjuk borttagning för behållare. Mer information om mjuk borttagning av behållare, inklusive hur du registrerar för för hands versionen finns i [mjuk borttagning för behållare (för hands version)](soft-delete-container-overview.md).

För data skydd från slut punkt till slut punkt rekommenderar Microsoft att du även aktiverar mjuk borttagning för blobbar och blob-versioner (för hands version). Information om hur du också aktiverar mjuk borttagning för blobbar finns i [Aktivera och hantera mjuk borttagning för blobbar](soft-delete-blob-enable.md). Information om hur du aktiverar BLOB-versioner finns i [BLOB-Versioning (för hands version)](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Aktivera mjuk borttagning av behållare

Du kan aktivera eller inaktivera mjuk borttagning av behållare för lagrings kontot när du vill genom att använda antingen Azure Portal eller en Azure Resource Manager mall.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följ dessa steg om du vill aktivera mjuk borttagning av behållare för ditt lagrings konto med hjälp av Azure Portal:

1. Navigera till ditt lagringskonto på [Azure-portalen](https://portal.azure.com/).
1. Leta upp inställningarna för **data skydd** under **BLOB service**.
1. Ange egenskapen för **mjuk borttagning av behållare** till *aktive rad*.
1. Under **bevarande principer**anger du hur länge borttagna behållare ska behållas genom att Azure Storage.
1. Spara ändringarna.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Skärm bild som visar hur du aktiverar mjuk borttagning av behållare i Azure Portal":::

# <a name="template"></a>[Mall](#tab/template)

Om du vill aktivera mjuk borttagning av behållare med en Azure Resource Manager mall skapar du en mall som anger egenskapen **containerDeleteRetentionPolicy** . Följande steg beskriver hur du skapar en mall i Azure Portal.

1. I Azure Portal väljer du **skapa en resurs**.
1. I **Sök på Marketplace**skriver du **mall distribution**och trycker sedan på **RETUR**.
1. Välj **malldistribution**, Välj **skapa**och välj sedan **skapa en egen mall i redigeraren**.
1. I redigeraren för mallar klistrar du in följande JSON. Ersätt  `<account-name>`   plats hållaren med namnet på ditt lagrings konto.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Ange kvarhållningsperioden. Standardvärdet är 7.
1. Spara mallen.
1. Ange kontots resurs grupp och välj sedan knappen **Granska + skapa**   för att distribuera mallen och aktivera mjuk borttagning av behållare.

## <a name="view-soft-deleted-containers"></a>Visa mjuka, borttagna behållare

När mjuk borttagning har Aktiver ATS kan du Visa mjuka, borttagna behållare i Azure Portal. Mjuka, borttagna behållare visas under den angivna kvarhållningsperioden. När kvarhållningsperioden går ut tas en mjuk borttagnings behållare bort permanent och visas inte längre.

Följ dessa steg om du vill visa mjuka, borttagna behållare i Azure Portal:

1. Navigera till ditt lagrings konto i Azure Portal och Visa listan över dina behållare.
1. Aktivera växeln Visa borttagna behållare för att inkludera borttagna behållare i listan.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Skärm bild som visar hur du visar mjuka borttagna behållare i Azure Portal":::

## <a name="restore-a-soft-deleted-container"></a>Återställa en mjuk borttagnings behållare

Du kan återställa en mjuk, borttagen behållare och dess innehåll inom kvarhållningsperioden. Följ dessa steg om du vill återställa en mjuk borttagnings behållare i Azure Portal:

1. Navigera till ditt lagrings konto i Azure Portal och Visa listan över dina behållare.
1. Visa snabb menyn för den behållare som du vill ta bort och välj **ångra borttagning** på menyn.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Skärm bild som visar hur du återställer en mjuk borttagnings behållare i Azure Portal":::

## <a name="next-steps"></a>Nästa steg

- [Mjuk borttagning för behållare (förhands granskning)](soft-delete-container-overview.md)
- [Mjuk borttagning för blobar](soft-delete-blob-overview.md)
- [Blob-version (för hands version)](versioning-overview.md)