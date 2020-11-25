---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018397"
---
Som minst rekommenderar vi att du använder minst 30 bilder per tagg i den första inlärnings uppsättningen. Du vill också samla in några extra bilder för att testa din modell när den har tränats.

Använd bilder med visuell sort för att träna modellen effektivt. Välj bilder som varierar av:
* kamera vinkel
* blixt
* bakgrund
* visuellt format
* enskilda/grupperade ämnen
* ikoner
* typ

Se dessutom till att alla dina utbildnings bilder uppfyller följande kriterier:
* . jpg-,. png-,. bmp-eller. gif-format
* inte större än 6 MB i storlek (4 MB för förutsägelse avbildningar)
* inte mindre än 256 bild punkter på den kortaste kanten; alla bilder som är kortare än detta kommer automatiskt att skalas upp av Custom Vision Service
