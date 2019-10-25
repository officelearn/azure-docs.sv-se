---
title: Självstudie – dela Azure spatiala ankare mellan sessioner och enheter med en Azure Cosmos DB server del | Microsoft Docs
description: I den här självstudien får du lära dig hur du delar identifierare för Azure spatial-ankare mellan Android/iOS-enheter i enhets trafik med en backend-tjänst och Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882169"
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

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Fortsätt till nästa självstudie om du vill veta mer om hur du använder Azures spatialdata i en ny enhet av HoloLens HoloLens.

> [!div class="nextstepaction"]
> [Starta en ny Unit HoloLens-app](./tutorial-new-unity-hololens-app.md)
