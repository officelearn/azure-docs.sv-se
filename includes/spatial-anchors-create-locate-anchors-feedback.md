---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694272"
---
## <a name="provide-feedback-to-the-user"></a>Ge feedback till användaren

Du kan skriva kod för att hantera sessionens uppdaterade händelse. Den här händelsen utlöses varje gång sessionen förbättrar förståelsen av din miljö. På så sätt kan du:

- Använd- `UserFeedback` klassen för att ge feedback till användaren när enheten flyttas och sessionen uppdaterar dess miljö förståelse. Gör
- Ta reda på i vilket ställe det finns tillräckligt med spårade spatialdata för att skapa avstånds ankare. Du fastställer detta med antingen `ReadyForCreateProgress` eller `RecommendedForCreateProgress`. En `ReadyForCreateProgress` gång är över 1 har vi tillräckligt med data för att spara moln rums ankare, men vi rekommenderar att du `RecommendedForCreateProgress` väntar tills är över 1 för att göra det.
