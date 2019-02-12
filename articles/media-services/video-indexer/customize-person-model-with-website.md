---
title: Använda Video Indexer-webbplats för att anpassa en Person modell – Azure
titlesuffix: Azure Media Services
description: Den här artikeln visar hur du anpassar en Person modell med Video Indexer-webbplatsen.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 29c32126a5bbe792dd6fe88ae189e09c511d2a22
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997075"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Anpassa en Person modell med Video Indexer-webbplats

Video Indexer stöder ansiktsigenkänning och kändisigenkänning för videoinnehåll. Funktionen för igenkänning av kändisar täcker ungefär en miljon ansikten baserat på vanliga datakälla, till exempel IMDB och Wikipedia övre LinkedIn Påverkare. Ansikten som inte känns igen av funktionen för igenkänning av kändisar identifieras. men de är kvar Namnlös. När du laddar upp din video till Video Indexer och få tillbaka resultat kan du gå tillbaka och namnge ansikten som inte tolkats. När du sätter en etikett ett ansikte med ett namn, läggs ansikte och namn till i ditt konto Person modellen. Video Indexer ser sedan den här ansikte i framtida videor och senaste videor.

Du kan använda Video Indexer-webbplatsen för att redigera ansikten som upptäcktes på en video, enligt beskrivningen i det här avsnittet. Du kan också använda API: et, enligt beskrivningen i [anpassa person modellen med hjälp av API: er](customize-person-model-with-api.md).

## <a name="edit-a-face"></a>Redigera ett ansikte

> [!NOTE]
> Namnen är unika för i en Person-modeller, så om du namnger två olika ytor samma namn, Video Indexer visar ansiktena som samma person och konvergerar dem när du indexera om videon. Om du ser att Video Indexer identifierat flera olika förekomster av samma sida, ge dem samma namn och indexera videon.
> Du kan uppdatera ett ansikte som Video Indexer kan identifieras som en kändisar med ett nytt namn. Det nya namnet som du ger företräde framför inbyggda kändisigenkänning.

1. Gå till [Video Indexer](https://www.videoindexer.ai/)-webbplatsen och logga in.
2. Sök efter en video som du vill visa och redigera i ditt konto.
3. Om du vill redigera ett ansikte i videon går du till den **Insights** fliken och klicka på pennikonen i det övre högra hörnet i fönstret.

    ![Anpassa person modell](./media/customize-face-model/customize-face-model.png)

4. Klicka på någon av de identifierade ansiktena och ändra deras namn från ”okänt #X” (eller det namn som tidigare tilldelats ansiktet).
5. Klicka på kryssikonen bredvid det nya namnet när du har skrivit i det nya namnet. Detta sparar det nya namnet och identifierar och namnge alla förekomster av den här ansikte i andra aktuella videor och i framtiden videor som du överför. Igenkänning av ansikte i dina aktuella videor kan ta lite tid att börja gälla eftersom det här är en batchprocess. 

    ![Spara uppdateringen](./media/customize-face-model/save-update.png)

## <a name="delete-a-face"></a>Ta bort ett ansikte

Om du vill ta bort en identifierad ansikte i videon, går du till den **Insights** fönstret och klicka på pennikonen i det övre högra hörnet i fönstret. Klicka på den **ta bort** alternativet under av de står inför. Detta tar bort som identifierats ansikte från videon. Ansiktet identifieras i de andra videor som de visas fortfarande, men du kan ta bort ansiktet från dessa videor samt när de har indexerats. Ansiktet fortsätter även att finns i ditt konto Person modell om du har gett den innan den tas bort.

## <a name="next-steps"></a>Nästa steg

[Anpassa Person modell med API: er](customize-person-model-with-api.md)
