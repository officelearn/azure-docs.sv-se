---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232494"
---
Du kan inte uppdatera platsen för en fästpunkt när den har skapats på tjänsten – du måste skapa ett nytt förtroendeankare och ta bort den gamla servern för att spåra en ny plats.

Om du inte behöver att hitta en fästpunkt för att uppdatera dess egenskaper kan du använda den `GetAnchorPropertiesAsync()` metod som returnerar en `CloudSpatialAnchor` objekt med egenskaper.
