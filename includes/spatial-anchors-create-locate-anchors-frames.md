---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694639"
---
## <a name="provide-frames-to-the-session"></a>Tillhandahåll ramar till sessionen

Den spatiala Anchor-sessionen fungerar genom att mappa utrymmet runt användaren. Detta hjälper till att avgöra var fäst punkter finns. Mobile Platforms (iOS & Android) kräver ett internt anrop till Camera-flödet för att hämta ramar från plattformens AR-bibliotek. Dessutom söker HoloLens ständigt igenom miljön, så det finns inget behov av ett särskilt samtal som på mobilen.