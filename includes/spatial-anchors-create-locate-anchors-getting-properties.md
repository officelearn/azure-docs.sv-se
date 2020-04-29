---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187054"
---
Du kan inte uppdatera platsen för en fäst punkt när den har skapats på tjänsten – du måste skapa ett nytt ankare och ta bort det gamla för att spåra en ny plats.

Om du inte behöver hitta en fäst punkt för att uppdatera dess egenskaper kan du använda `GetAnchorPropertiesAsync()` metoden, som returnerar ett `CloudSpatialAnchor` objekt med egenskaper.
