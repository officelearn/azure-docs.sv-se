---
title: Dokumentinformation - Anpassad översättare
titleSuffix: Azure Cognitive Services
description: På dokumentlistsidan visas de första 10 dokumenten på arbetsytan. För vart och ett av dokumenten visas namn, parkoppling, typ, språk, uppladdningstidsstämpel och e-postadressen till den användare som laddade upp dokumentet.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf0d96414c40784210723e315da5d885d61198c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595590"
---
# <a name="view-document-details"></a>Visa dokumentinformation

På dokumentlistsidan visas de första 10 dokumenten på arbetsytan. För vart och ett av dokumenten visas namn, parkoppling, typ, språk, uppladdningstidsstämpel och e-postadressen till den användare som laddade upp dokumentet.

Klicka på ett enskilt dokument för att visa sidan med dokumentinformation. På sidan med dokumentinformation visas listan över extraherade meningar från dokumentet.

- Som standard är källspråket markerat i listrutan, men du kan växla för att visa meningar på målspråket.
- 20 meningar visas som standard per sida. Du kan använda sidnumreringskontrollen för att bläddra mellan sidor.

![information om dokument](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Ta bort ett dokument

Användaren måste vara arbetsyteägare för att kunna ta bort dokumentet för att ett dokument ska kunna tas bort. Om ett dokument används av en modell, som ingår i någon del av utbildningsprocessen eller någon del av distributionsprocessen, kan dokumentet inte tas bort.

1. Gå till dokumentsidan
2.  Hovra på alla dokument post och klicka på papperskorgen ikonen.

    ![Ta bort dokument](media/how-to/how-to-delete-document-1.png)

3.  Bekräfta borttagning.

    ![Ta bort bekräfta](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Nästa steg

- Läs om hur du [tränar en modell](how-to-train-model.md).
