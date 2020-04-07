---
title: Exempel modeller
description: Visar källor för exempelmodeller.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679487"
---
# <a name="sample-models"></a>Exempel modeller

I den här artikeln visas några resurser för exempeldata som kan användas för att testa Azure Remote Rendering-tjänsten.

## <a name="built-in-sample-model"></a>Inbyggd exempelmodell

Vi tillhandahåller en inbyggd exempelmodell som alltid kan läsas in med hjälp av **URL-builtin://Engine**

![Exempelmodell](./media/sample-model.png "Exempelmodell")

Modellstatistik:

| Namn | Värde |
|-----------|:-----------|
| [Obligatorisk vm-storlek](../how-tos/session-rest-api.md#create-a-session) | standard |
| Antal trianglar | 18,7 miljoner |
| Antal rörliga delar | 2073 |
| Antal material | 94 |

## <a name="third-party-data"></a>Uppgifter från tredje part

Khronos-koncernen har en uppsättning glTF-exempelmodeller för testning. ARR stöder glTF-formatet både i text (*.gltf*) och i binär (*.glb*) form. Vi föreslår att du använder PBR-modellerna för bästa visuella resultat:

* [glTF-exempelmodeller](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Återge en modell med Unity](../quickstarts/render-model.md)
* [Snabbstart: Konvertera en modell för rendering](../quickstarts/convert-model.md)
