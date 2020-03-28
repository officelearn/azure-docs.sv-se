---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130120"
---
Som ett minimum rekommenderar vi att du använder minst 30 bilder per tagg i den inledande träningsuppsättningen. Du vill också samla några extra bilder för att testa din modell när den är tränad.

För att träna din modell effektivt, använd bilder med visuell variation. Välj bilder som varierar med:
* Kameravinkel
* Belysning
* bakgrund
* visuell stil
* enskilda/grupperade ämnen
* size
* typ

Kontrollera dessutom att alla dina träningsbilder uppfyller följande kriterier:
* .jpg-, .png-, .bmp- eller .gif-format
* inte större än 6 MB i storlek (4 MB för förutsägelsebilder)
* inte mindre än 256 pixlar på kortaste kanten. bilder som är kortare än så skalas automatiskt upp av Custom Vision-tjänsten
