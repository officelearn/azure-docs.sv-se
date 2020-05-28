---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998106"
---
Nästa steg är att konfigurera appen så att den använder din konto identifierare och konto nyckeln. Du kopierade dem till en text redigerare när du [ställer in resursen för spatiala ankare](#create-a-spatial-anchors-resource).

I fönstret **projekt** navigerar du till `Assets\AzureSpatialAnchors.SDK\Resources` . Välj `SpatialAnchorConfig`. I fönstret **kontrollant** anger du sedan `Account Key` värdet för `Spatial Anchors Account Key` och `Account ID` som värdet för `Spatial Anchors Account Id` .

Sedan öppnar du `SpatialAnchorManager.cs` . Sök efter `CreateSessionAsync()` och Lägg till följande rad och ersätt i din konto domän från tidigare: `session.Configuration.AccountDomain = "MyAccountDomain";` . Du kan lägga till den här raden direkt före den här kommentaren `// Configure authentication` .
