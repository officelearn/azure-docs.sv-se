---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "67187054"
---
Du kan inte uppdatera platsen för en fäst punkt när den har skapats på tjänsten – du måste skapa ett nytt ankare och ta bort det gamla för att spåra en ny plats.

Om du inte behöver hitta en fäst punkt för att uppdatera dess egenskaper kan du använda `GetAnchorPropertiesAsync()` metoden, som returnerar ett `CloudSpatialAnchor` objekt med egenskaper.
