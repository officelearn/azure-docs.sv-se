---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605979"
---
<!--Create a media services asset CLI-->

Följande Azure CLI-kommando skapar en ny Media Services till gång. Ersätt värdena `assetName` `amsAccountName` och `resourceGroup` med värden som du håller på att arbeta med.  

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
