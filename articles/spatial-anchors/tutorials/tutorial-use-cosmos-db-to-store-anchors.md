---
title: 'Självstudiekurs: Dela ankare med Azure Cosmos DB'
description: I den här självstudien får du lära dig hur du delar Azure Spatial Anchors-identifierare över Android/iOS-enheter i Unity med en backend-tjänst och Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77615156"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Självstudiekurs: Dela Azure Spatial Anchors över sessioner och enheter med en Azure Cosmos DB-backdel

Den här självstudien är en fortsättning [på att dela Azure Spatial Anchors över sessioner och enheter.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) Det guidar dig genom processen att lägga till några fler funktioner för att göra Azure Cosmos DB fungera som backend-lagring samtidigt som du delar azure spatial ankare över sessioner och enheter.

![GIF som illustrerar objektets persistens](./media/persistence.gif)

Det är värt att notera att även om du kommer att använda Unity och Azure Cosmos DB i den här självstudien, är det bara för att ge dig ett exempel på hur du delar Spatial Anchors-identifierare över enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat. Dessutom kräver ASP.NET Core-webbappen som används i den här självstudien .NET Core 2.2 SDK. Det går bra på Web Apps för Windows, men det kommer för närvarande inte köras på Web Apps för Linux.

## <a name="create-a-database-account"></a>Skapa ett databaskonto

Lägg till en Azure Cosmos-databas i resursgruppen som du skapade tidigare.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopiera `Connection String` eftersom du behöver det.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Göra mindre ändringar i SharingService-filerna

Öppna i Solution `SharingService\Startup.cs` **Explorer**.

Leta `#define INMEMORY_DEMO` upp högst upp i filen och kommentera den raden ut. Spara filen.

Öppna i Solution `SharingService\appsettings.json` **Explorer**.

Leta `StorageConnectionString` reda på egenskapen och ange att `Connection String` värdet ska vara detsamma som det värde som du kopierade i [steget Skapa ett databaskonto](#create-a-database-account). Spara filen.

Du kan publicera delningstjänsten igen och köra exempelappen.

## <a name="troubleshooting"></a>Felsökning

### <a name="unity-20193"></a>Enhet 2019.3

På grund av att förändringar bryts stöds för närvarande inte Unity 2019.3. Använd Unity 2019.1 eller 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Om du vill veta mer om hur du använder Azure Spatial Anchors i en ny Unity HoloLens-app fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Starta en ny Unity HoloLens-app](./tutorial-new-unity-hololens-app.md)
