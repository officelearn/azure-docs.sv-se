---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006467"
---
## <a name="provide-frames-to-the-session"></a>Tillhandahåll ramar till sessionen

Den spatiala Anchor-sessionen fungerar genom att mappa utrymmet runt användaren. Detta hjälper till att avgöra var fäst punkter finns. Mobile Platforms (iOS & Android) kräver ett internt anrop till Camera-flödet för att hämta ramar från plattformens AR-bibliotek. Dessutom söker HoloLens hela tiden igenom miljön, så det finns inget behov av ett särskilt samtal som på de mobila plattformarna.