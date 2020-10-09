---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "67187054"
---
Du kan inte uppdatera platsen för en fäst punkt när den har skapats på tjänsten – du måste skapa ett nytt ankare och ta bort det gamla för att spåra en ny plats.

Om du inte behöver hitta en fäst punkt för att uppdatera dess egenskaper kan du använda `GetAnchorPropertiesAsync()` metoden, som returnerar ett `CloudSpatialAnchor` objekt med egenskaper.
