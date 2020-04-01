---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694272"
---
## <a name="provide-feedback-to-the-user"></a>Ge feedback till användaren

Du kan skriva kod för att hantera den session som uppdateras händelsen. Denna händelse bränder varje gång sessionen förbättrar sin förståelse av din omgivning. På så sätt kan du:

- Använd `UserFeedback` klassen för att ge feedback till användaren när enheten flyttas och sessionen uppdaterar sin miljöförståelse. Gör
- Bestäm vid vilken tidpunkt det finns tillräckligt med spårade spatialdata för att skapa rumsliga ankare. Du bestämmer `ReadyForCreateProgress` detta `RecommendedForCreateProgress`med antingen eller . När `ReadyForCreateProgress` är över 1, vi har tillräckligt med data för att `RecommendedForCreateProgress` spara ett moln rumsligt ankare, men vi rekommenderar att du väntar tills är över 1 för att göra det.
