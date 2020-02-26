---
title: Flytta din tjänst resurs över flera regioner
titleSuffix: Azure Cognitive Search
description: I den här artikeln visas hur du flyttar dina Azure Kognitiv sökning-resurser från en region till en annan i Azure-molnet.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/18/2020
ms.openlocfilehash: 392c86d8ea24e59d388926d4df581305ea2b531d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599306"
---
# <a name="move-your-azure-cognitive-search-service-to-another-azure-region"></a>Flytta din Azure Kognitiv sökning-tjänst till en annan Azure-region

Om du vill flytta ditt Azure-konto för kognitiva tjänster från en region till en annan skapar du en export-mall för att flytta dina prenumerationer. När du har flyttat din prenumeration måste du flytta dina data och återskapa tjänsten.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Exportera en mall.
> * Ändra mallen: Lägg till mål regionen, Sök-och lagrings konto namnen.
> * Distribuera mallen för att skapa de nya Sök-och lagrings kontona.
> * Verifiera din tjänst status i den nya regionen
> * Rensa resurser i käll regionen.

## <a name="prerequisites"></a>Förutsättningar

- Kontrol lera att tjänsterna och funktionerna som ditt konto använder stöds i mål regionen.

- För för hands versions funktioner ser du till att din prenumeration är vit listas för mål regionen. Mer information om förhands gransknings funktioner finns i [kunskaps lager](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro), [stegvis berikning](https://docs.microsoft.com/azure/search/cognitive-search-incremental-indexing-conceptual)och [privat slut punkt](https://docs.microsoft.com/azure/search/service-create-private-endpoint).

## <a name="assessment-and-planning"></a>Utvärdering och planering

När du flyttar din Sök tjänst till den nya regionen måste du [flytta dina data till den nya lagrings tjänsten](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#configure-the-new-storage-account) och sedan återskapa dina index, färdighetsuppsättningar och kunskaps lager. Du bör registrera aktuella inställningar och kopiera JSON-filer för att göra det enklare och snabbare att skapa en tjänst.

## <a name="moving-your-search-services-resources"></a>Flytta resurser för Sök tjänsten

Du börjar med att exportera och sedan ändra en Resource Manager-mall.

### <a name="export-a-template"></a>Exportera en mall

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Gå till din resurs grupps sida.

> [!div class="mx-imgBorder"]
> exempel på ![resurs grupps sida](./media/search-move-resource/export-template-sample.png)

3. Välj **Alla resurser**.

3. I den vänstra navigerings menyn väljer du **Exportera mall**.

4. Välj **Hämta** på sidan **Exportera mall** .

5. Leta upp zip-filen som du laddade ned från portalen och zippa upp filen till en valfri mapp.

Zip-filen innehåller de. JSON-filer som utgör mallen och skripten för att distribuera mallen.

### <a name="modify-the-template"></a>Ändra mallen

Du ändrar mallen genom att ändra Sök-och lagrings konto namn och-regioner. Namnen måste följa reglerna för varje tjänst och region namngivnings konventioner. 

Information om hur du hämtar koder för regions platser finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regions namnet utan blank steg, **Central usa** = **centrala**.

1. I Azure-portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution** och trycker sedan på **RETUR**.

3. Välj **Malldistribution**.

4. Välj **Skapa**.

5. Välj alternativet för att **skapa din egen mall i redigeringsprogrammet**.

6. Välj **Läs in fil**och följ sedan anvisningarna för att läsa in filen **Template. JSON** som du laddade ned och zippa upp i föregående avsnitt.

7. I filen **Template. JSON** namnger du mål Sök-och lagrings kontona genom att ange standardvärdet för Sök-och lagrings konto namn. 

8. Redigera egenskapen **location** i filen **Template. JSON** till mål regionen för både Sök-och lagrings tjänster. I det här exemplet anges mål regionen till `centralus`.

```json
},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Search/searchServices",
            "apiVersion": "2015-08-19",
            "name": "[parameters('searchServices_target_region_search_name')]",
            "location": "centralus",
            "sku": {
                "name": "standard"
            },
            "properties": {
                "replicaCount": 1,
                "partitionCount": 1,
                "hostingMode": "Default"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[parameters('storageAccounts_tagetstorageregion_name')]",
            "location": "centralus",
            "sku": {
                "name": "Standard_RAGRS",
                "tier": "Standard"
            },
```

### <a name="deploy-the-template"></a>Distribuera mallen

1. Spara filen **Template. JSON** .

2. Ange eller Välj egenskaps värden:

- **Prenumeration**: Välj en Azure-prenumeration.

- **Resursgrupp**: Välj **Skapa ny** och ge resursgruppen ett namn.

- **Plats**: Välj en Azure-plats.

3. Klicka på kryss rutan **Jag accepterar villkoren som anges ovan** och klicka sedan på knappen **Välj inköp** .

## <a name="verifying-your-services-status-in-new-region"></a>Verifiera tjänsternas status i ny region

Om du vill kontrol lera flyttningen öppnar du den nya resurs gruppen och dina tjänster visas i listan med den nya regionen.

Om du vill flytta dina data från käll regionen till mål regionen kan du läsa artikeln rikt linjer för att [flytta dina data till det nya lagrings kontot](https://docs.microsoft.com/azure/storage/common/storage-account-move?tabs=azure-portal#move-data-to-the-new-storage-account).

## <a name="clean-up-resources-in-your-original-region"></a>Rensa resurser i din ursprungliga region

Ta bort käll tjänst kontot för att genomföra ändringarna och slutföra flyttningen av ditt tjänst konto.

## <a name="next-steps"></a>Nästa steg

[Skapa ett index](https://docs.microsoft.com/azure/search/search-get-started-portal)

[Skapa en färdigheter](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob)

[Skapa ett kunskaps lager](https://docs.microsoft.com/azure/search/knowledge-store-create-portal)

