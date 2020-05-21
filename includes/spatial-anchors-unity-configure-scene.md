---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: e3ef7e7b8f527cde1fcfbb13141fd3a520921267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673432"
---
Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

I fönstret **projekt** navigerar du till `Assets\AzureSpatialAnchors.SDK\Resources` . Välj `SpatialAnchorConfig`. I fönstret **kontrollant** anger du sedan `Account Key` värdet för `Spatial Anchors Account Key` och `Account ID` som värdet för `Spatial Anchors Account Id` .

Sedan öppnar du `SpatialAnchorManager.cs` . Sök efter `CreateSessionAsync()` och Lägg till följande rad och ersätt i din konto domän från tidigare: `session.Configuration.AccountId = "MyAccountDomain";` . Du kan lägga till den här raden direkt före den här kommentaren `// Configure authentication` .
