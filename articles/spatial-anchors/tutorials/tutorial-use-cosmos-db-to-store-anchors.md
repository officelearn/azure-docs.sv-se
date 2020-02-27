---
title: 'Självstudie: dela ankare med Azure Cosmos DB'
description: I den här självstudien får du lära dig hur du delar identifierare för Azure spatial-ankare mellan Android/iOS-enheter i enhets trafik med en backend-tjänst och Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615156"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Självstudie: dela Azure spatiala ankare mellan sessioner och enheter med en Azure Cosmos DB server del

Den här självstudien är en fortsättning av [delning av Azures avstånds ankare mellan sessioner och enheter.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Det vägleder dig genom processen att lägga till några fler funktioner för att göra Azure Cosmos DB fungerar som backend-lagring medan du delar upp Azure-spatialdata mellan sessioner och enheter.

![GIF som illustrerar objekt beständighet](./media/persistence.gif)

Det är värt att Observera att även om du kommer att använda Unity och Azure Cosmos DB i den här självstudien, är det bara att ge dig ett exempel på hur du kan dela spatiala ankare med identifierare på olika enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat. Dessutom kräver ASP.NET Core-webbappen som används i den här självstudien .NET Core 2,2 SDK. Det fungerar bra på Web Apps för Windows, men körs för närvarande inte på Web Apps för Linux.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Lägg till en Azure Cosmos-databas i resurs gruppen som du skapade tidigare.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopiera `Connection String` eftersom du behöver den.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Gör mindre ändringar i SharingService-filerna

Öppna `SharingService\Startup.cs`i **Solution Explorer**.

Leta upp `#define INMEMORY_DEMO` överst i filen och kommentera raden. Spara filen.

Öppna `SharingService\appsettings.json`i **Solution Explorer**.

Leta upp egenskapen `StorageConnectionString` och Ställ in värdet på samma sätt som det `Connection String` värdet som du kopierade i [steget Skapa ett databas konto](#create-a-database-account). Spara filen.

Du kan publicera delnings tjänsten igen och köra exempel appen.

## <a name="troubleshooting"></a>Felsökning

### <a name="unity-20193"></a>Unity 2019,3

På grund av avbrytande ändringar stöds inte Unity 2019,3 för närvarande. Använd Unity 2019,1 eller 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Fortsätt till nästa självstudie om du vill veta mer om hur du använder Azures spatialdata i en ny enhet av HoloLens HoloLens.

> [!div class="nextstepaction"]
> [Starta en ny Unit HoloLens-app](./tutorial-new-unity-hololens-app.md)
