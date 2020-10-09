---
ms.openlocfilehash: 849777ac3ec11915c602861fea0c42a49fb08bcb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006467"
---
## <a name="provide-frames-to-the-session"></a>Tillhandahåll ramar till sessionen

Den spatiala Anchor-sessionen fungerar genom att mappa utrymmet runt användaren. Detta hjälper till att avgöra var fäst punkter finns. Mobile Platforms (iOS & Android) kräver ett internt anrop till Camera-flödet för att hämta ramar från plattformens AR-bibliotek. Dessutom söker HoloLens hela tiden igenom miljön, så det finns inget behov av ett särskilt samtal som på de mobila plattformarna.