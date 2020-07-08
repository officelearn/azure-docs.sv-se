---
title: Hantera Azure Maps skapare
description: I den här artikeln får du lära dig hur du hanterar Azure Maps Creator.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 579294388dbcf9f785ef41e06505c14b6767565f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83677936"
---
# <a name="manage-azure-maps-creator"></a>Hantera Azure Maps skapare

Med Azure Maps Creator kan du skapa privat indata från kartan. Med hjälp av Azure Maps API och modulen kartor kan du utveckla interaktiva och dynamiska kart webb program. För närvarande är Creator endast tillgängligt i USA med hjälp av pris nivån S1.

Den här artikeln tar dig igenom stegen för att skapa och ta bort en skapare-resurs i ett Azure Maps konto.

## <a name="create-creator-resource"></a>Skapa skapare resurs

1. Logga in på [Azure Portal](https://portal.azure.com)

2. Välj ditt Azure Maps-konto. Om du inte kan se ditt Azure Maps konto under de **senaste resurserna**går du till Azure Portal-menyn. Välj **Alla resurser**. Sök efter och välj ditt Azure Maps-konto.

    ![Azure Maps portalens start sida](./media/how-to-manage-creator/select-maps-account.png)

3. När du är på sidan Azure Maps konto går du till **översikts** alternativet under **skapare**. Klicka på **skapa** för att skapa en Azure Maps skapare-resurs.

    ![Sidan Skapa Azure Maps skapare](./media/how-to-manage-creator/creator-blade-settings.png)

4. Ange namn och plats för din skapare resurs. För närvarande stöds endast skapare i USA. Klicka på **Granska + skapa**.

   ![Sidan Ange skapare konto information](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Granska inställningarna och klicka på **skapa**.

    ![Sidan Bekräfta inställningar för skapare av konto](./media/how-to-manage-creator/creator-create-dialog.png)

6. När distributionen är klar visas en sida med ett lyckat eller ett fel meddelande.

   ![Status sida för resurs distribution](./media/how-to-manage-creator/creator-resource-created.png)

7. Klicka på **Gå till resurs**. Sidan skapare resurs visning visar status för din skapare-resurs och den valda demografiska regionen.

    ![Sidan status för skapare](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >På sidan skapare resurs kan du gå tillbaka till det Azure Maps konto som den tillhör genom att klicka på Azure Maps konto.

## <a name="delete-creator-resource"></a>Ta bort skapare resurs

Om du vill ta bort skapare-resursen går du till ditt Azure Maps-konto. Välj **Översikt** under **skapare**. Klicka på knappen **Ta bort**.

>[!WARNING]
>När du tar bort skapare-resursen för ditt Azure Maps-konto tar du även bort data uppsättningarna, tilesets och funktionen statesets som skapats med hjälp av Creator Services.

![Sidan skapare med knappen Ta bort](./media/how-to-manage-creator/creator-delete.png)

Klicka på knappen **ta bort** och skriv namnet på din skapare för att bekräfta borttagningen. När resursen har tagits bort visas en bekräftelse sida, som i bilden nedan:

![Sidan skapare med bekräftelse på borttagning](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Autentisering

Skapare ärver inställningar för Azure Maps Access Control (IAM). Alla API-anrop för data åtkomst måste skickas med regler för autentisering och auktorisering.

Skapare användnings data ingår i Azure Maps användnings diagram och aktivitets loggen.  Mer information finns i [hantera autentisering i Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Åtkomst till Creator Services

Creator-tjänsterna är bara tillgängliga från den plats som valdes när den skapas. Om anrop görs till skapare av tjänster utanför den valda platsen returneras ett användar fel meddelande. För att ringa från platser utanför den valda platsen måste tjänstens URL innehålla det geografiska prefixet för de valda platserna. Om skapare till exempel skapas i USA, måste alla anrop till konverterings tjänsten skickas till `us.atlas.microsoft.com/conversion/convert` .

Alla data som importeras till skaparen bör också överföras till samma geografiska plats som skaparen-resursen. Om skapare till exempel är etablerad i USA, ska alla rå data överföras via `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Nästa steg

Introduktion till skapare för från koppling till inomhus:

> [!div class="nextstepaction"]
> [Data uppladdning](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Data konvertering](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Data uppsättning](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Funktions tillstånds uppsättning](creator-indoor-maps.md#feature-statesets)

Lär dig hur du använder skaparen för att rendera inaktuella kartor i ditt program:

> [!div class="nextstepaction"]
> [Själv studie kurs om Azure Maps Creator](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamisk formatering för inomhus-karta](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Använda modulen inomhus Maps](how-to-use-indoor-module.md)