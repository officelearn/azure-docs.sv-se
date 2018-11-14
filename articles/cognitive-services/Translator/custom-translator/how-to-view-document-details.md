---
title: Dokumentinformation - anpassad Translator
titleSuffix: Azure Cognitive Services
description: Dokumentsidan visar de första 10 dokumentet på din arbetsyta. För vart och ett av dokumenten visas namnet, parkoppling, typ, språk, ladda upp tidsstämpel och e-postadressen för den användare som har överförts dokumentet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: how to edit a model
ms.openlocfilehash: fe108831f173eb31af79a2f8e5f7faf57015c38a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627631"
---
# <a name="view-document-details"></a>Visa dokumentinformation

Dokumentsidan visar de första 10 dokumentet på din arbetsyta. För vart och ett av dokumenten visas namnet, parkoppling, typ, språk, ladda upp tidsstämpel och e-postadressen för den användare som har överförts dokumentet.

Klicka på ett enskilt dokument att visa dokumentsidan. Dokumentsidan visar lista över extraherade meningar från dokumentet.

- Som standard ”källa” språk har valts i fältet listrutan, men du kan växla om du vill visa meningar på språket som mål. 
- 20 meningar visas per sida som standard. Du kan använda sidbrytning kontrollen för att navigera mellan sidor.

![dokumentinformation](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Ta bort ett dokument

Användaren måste vara någon som äger arbetsyta att ta bort dokument om du vill ta bort ett dokument. Även om ett dokument används av en modell som finns i någon del av processen utbildning eller någon del av distributionsprocessen, dokumentet kan inte tas bort.

1. Gå till dokumentsidan för
2.  Hovra över en post, dokument och klicka på Papperskorgen.

    ![Ta bort dokument](media/how-to/how-to-delete-document-1.png)

3.  Bekräfta borttagning.

    ![Bekräftelse på borttagning](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig [hur du tränar en modell](how-to-train-model.md).
