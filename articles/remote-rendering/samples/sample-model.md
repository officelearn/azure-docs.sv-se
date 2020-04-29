---
title: Exempelmodeller
description: Visar en lista över källor för exempel modeller.
author: florianborn71
ms.author: flborn
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: 0c28d36c7934fbbac0ddd11562c8dc237b0360ee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679487"
---
# <a name="sample-models"></a>Exempelmodeller

I den här artikeln visas några resurser för exempel data som kan användas för att testa tjänsten Azure Remote rendering.

## <a name="built-in-sample-model"></a>Inbyggd exempel modell

Vi tillhandahåller en inbyggd exempel modell som alltid kan läsas in med URL- **Builtin://Engine**

![Exempel modell](./media/sample-model.png "Exempel modell")

Modell statistik:

| Name | Värde |
|-----------|:-----------|
| [Nödvändig VM-storlek](../how-tos/session-rest-api.md#create-a-session) | standard |
| Antal trianglar | 18 700 000 |
| Antal rörliga delar | 2073 |
| Antal material | 94 |

## <a name="third-party-data"></a>Data från tredje part

Khronos-gruppen har en uppsättning glTF-exempel modeller för testning. ARR stöder glTF-formatet i text-(*. glTF*) och i binärformat (*. GLB*) form. Vi rekommenderar att du använder PBR-modellerna för bästa visuella resultat:

* [glTF exempel modeller](https://github.com/KhronosGroup/glTF-Sample-Models)

## <a name="next-steps"></a>Nästa steg

* [Snabb start: rendera en modell med Unity](../quickstarts/render-model.md)
* [Snabb start: konvertera en modell för åter givning](../quickstarts/convert-model.md)
