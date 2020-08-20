---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: 820353ffbb5e23e27c3039ad27a8f6507b9d2167
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608892"
---
<!--Create a media services asset REST-->

Följande Azure .NET-kommando skapar en ny Media Services till gång. Ersätt värdena `subscriptionID` , `resourceGroup` och `amsAccountName` med värden som du håller på att arbeta med. Ge din till gång ett namn genom att ställa in det `assetName` här.

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```
