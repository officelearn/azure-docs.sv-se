---
title: Med hjälp av taggar i Azure Content kontrollant | Microsoft Docs
description: Innehåll kontrollant innehåller standardtaggar och du kan skapa anpassade taggar för att kontrollera innehåll som är specifika för din verksamhet.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: add4c685c07c63944ae89f48a47ac78df28c1623
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351522"
---
# <a name="about-tags"></a>Om etiketter #

Du kan skapa anpassade taggar för mer riktade genomsökning utöver de två standardtaggar (en-isadult och r--isracy). Dessa anpassade taggar är sedan tillgängliga för mänsklig granskare att tilldela bilder eller text.

## <a name="create-tags"></a>Skapa taggar ##

1.  Markera taggar på fliken Inställningar.

  ![Innehåll Avbrottsmoderering taggar](images/tags-1.png)

2.  Ange en kort kod två bokstäver för taggen.
3.  Ange ett namn för taggen. Behåll namnet korta och beskrivande. Till exempel ”isNudity”.
4.  Ange en beskrivning.
5.  Klicka på Add (Lägg till).
6.  Klicka på Spara när du är klar att skapa etiketter.

![Definiera innehållet Avbrottsmoderering taggar](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Med hjälp av anpassade taggar ##

Anpassade taggar används under mänsklig granskning. De visas på förhandsversionen och granskaren markeras den genom att klicka på den.

![Med hjälp av innehåll Avbrottsmoderering taggar](images/tags-3-use.png)

Du kan stänga av olika taggar för olika granskningar genom att markera eller avmarkera är synliga.
 
![Inaktivering av innehåll Avbrottsmoderering taggar](images/tags-4-disable.png)

Du kan inte ta bort två standardtaggar, isadult och isracy, kan du ta bort alla anpassade taggar som du har definierat. Klicka på skräp kan bredvid den tagg som du vill ta bort.

![Ta bort innehåll Avbrottsmoderering taggar](images/tags-5-delete.png)

## <a name="next-steps"></a>Nästa steg ##

Om du vill lära dig mer om att använda taggar för avbildningen avbrottsmoderering, se [hur du granskar kontrollerad bilder](Review-Moderated-Images.md).
