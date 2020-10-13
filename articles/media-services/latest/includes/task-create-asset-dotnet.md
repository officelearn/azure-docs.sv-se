---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: dotnet
ms.openlocfilehash: dcd2cda3bad2a13a83c5f3f6700e5a57471e2065
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653906"
---
<!--Create a media services asset REST-->

Följande Azure .NET-kommando skapar en ny Media Services till gång. Ersätt värdena `subscriptionID` , `resourceGroup` och `amsAccountName` med värden som du håller på att arbeta med. Ge din till gång ett namn genom att ställa in det `assetName` här.

[!code-csharp[Main](../../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]
