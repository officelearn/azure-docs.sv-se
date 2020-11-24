---
title: 'Självstudie: dela ankare med Azure Cosmos DB'
description: I den här självstudien får du lära dig hur du delar identifierare för Azure spatial-ankare mellan Android/iOS-enheter i enhets trafik med en backend-tjänst och Azure Cosmos DB.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ff888cd98cc79f3e2d508b01f092102eaa038c86
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95494768"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Självstudie: dela Azure spatiala ankare mellan sessioner och enheter med en Azure Cosmos DB server del

Den här självstudien är en fortsättning av [delning av Azures avstånds ankare mellan sessioner och enheter.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Det vägleder dig genom processen att lägga till några fler funktioner för att göra Azure Cosmos DB fungerar som backend-lagring medan du delar upp Azure-spatialdata mellan sessioner och enheter.

![GIF som illustrerar objekt beständighet](./media/persistence.gif)

Det är värt att Observera att även om du kommer att använda Unity och Azure Cosmos DB i den här självstudien, är det bara att ge dig ett exempel på hur du kan dela spatiala ankare med identifierare på olika enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Lägg till en Azure Cosmos-databas i resurs gruppen som du skapade tidigare.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopiera `Connection String` eftersom du behöver den.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Gör mindre ändringar i SharingService-filerna

Öppna i **Solution Explorer** `SharingService\Startup.cs` .

Leta upp `#define INMEMORY_DEMO` överst i filen och kommentera raden. Spara filen.

Öppna i **Solution Explorer** `SharingService\appsettings.json` .

Leta upp `StorageConnectionString` egenskapen och Ställ in värdet på samma sätt som det `Connection String` värde som du kopierade i [steget Skapa ett databas konto](#create-a-database-account). Spara filen.

Du kan publicera delnings tjänsten igen och köra exempel appen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Fortsätt till nästa självstudie om du vill veta mer om hur du använder Azures spatialdata i en ny enhet av HoloLens HoloLens.

> [!div class="nextstepaction"]
> [Starta en ny Unity HoloLens-app](./tutorial-new-unity-hololens-app.md)
