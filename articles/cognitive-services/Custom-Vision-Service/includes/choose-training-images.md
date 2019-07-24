---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423572"
---
Som minst rekommenderar vi att du använder minst 30 bilder per tagg i den första inlärnings uppsättningen. Du vill också samla in några extra bilder för att testa din modell när den har tränats.

Använd bilder med visuell sort för att träna modellen effektivt. Välj bilder som varierar av:
* kamera vinkel
* blixt
* Lägg
* visuellt format
* enskilda/grupperade ämnen
* size
* type

Se dessutom till att alla dina utbildnings bilder uppfyller följande kriterier:
* . jpg-,. png-eller. bmp-format
* inte större än 6 MB i storlek (4 MB för förutsägelse avbildningar)
* inte mindre än 256 bild punkter på den kortaste kanten; alla bilder som är kortare än detta kommer automatiskt att skalas upp av Custom Vision Service