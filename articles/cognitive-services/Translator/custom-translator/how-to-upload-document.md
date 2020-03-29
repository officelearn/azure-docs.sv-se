---
title: Så här laddar du upp ett dokument - Custom Translator
titleSuffix: Azure Cognitive Services
description: Dokumentuppladdningsfunktionen överför parallella dokument (två dokument där det ena är ursprunget och det andra är översättningen) till tjänsten.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: b2a249a40d8c782d54a12df43d33655f3409753c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647390"
---
# <a name="upload-a-document"></a>Ladda upp ett dokument

I [Custom Translator](https://portal.customtranslator.azure.ai)kan du ladda upp parallella dokument för att träna dina översättningsmodeller. [Parallella dokument](what-are-parallel-documents.md) är par av dokument där det ena är en översättning av den andra. Ett dokument i paret innehåller meningar på källspråket och det andra dokumentet innehåller dessa meningar översatta till målspråket.

Innan du laddar upp dokumenten bör du läsa [dokumentformaten och namngivningskonventionen](document-formats-naming-convention.md) för att kontrollera att filformatet stöds i Anpassad översättare.

## <a name="how-to-upload-document"></a>Hur laddar man upp dokument?

Från [Custom Translator](https://portal.customtranslator.azure.ai) portal, klicka på "Dokument" fliken för att gå till dokument sida.

![Länk för dokumentuppladdning](media/how-to/how-to-upload-1.png)


1.  Klicka på knappen Ladda upp filer på dokumentsidan.

    ![Sidan Ladda upp dokument](media/how-to/how-to-upload-2.png)

2.  I dialogrutan fylls följande information i:

    a.  Dokumenttyp:

    -  Utbildning: Dessa dokument kommer att användas för utbildning set.
    -  Tuning: Dessa dokument används för justeringsuppsättning.
    -  Testning: Dessa dokument används för testuppsättning.
    -  Frasordlista: Dessa dokument används för frasordlista.
    -  Meningsordlista: Dessa dokument används för meningsordlista

    b.  Språkpar

    c.  Åsidosätt dokument om det finns: Markera den här kryssrutan om du vill skriva över befintliga dokument med samma namn.

    d.  Fyll i det relevanta avsnittet för antingen parallella data eller kombinationsdata.

    -  Parallella data:
        -  Källfil: Välj källspråkfil från den lokala datorn.
        -  Målfil: Välj målspråksfil från den lokala datorn.
        -  Dokumentnamn: Används bara om du laddar upp parallella filer.

    - Kombinationsdata:
        -  Kombinationsfil: Välj kombinationsfilen från den lokala datorn. Kombinationsfilen har både käll- och målspråkmeningar. [Namngivningskonvention](document-formats-naming-convention.md) är viktigt för kombinationsfiler.

    e.  Klicka på Ladda upp

    ![Dialogrutan Ladda upp dokument](media/how-to/how-to-upload-dialog.png)

3.  Nu bearbetar vi dina dokument och försöker extrahera meningar. Du kan klicka på "Visa förlopp för överföring" för att kontrollera status för dina dokument när de bearbetar.

    ![Dialogrutan Ladda upp dokumentbearbetning](media/how-to/how-to-upload-processing-dialog.png)

4.  På den här sidan visas status och eventuella fel för varje fil i uppladdningen. Du kan när som helst visa tidigare uppladdningsstatus genom att klicka på fliken "Uppladdningshistorik".

    ![Dialogrutan Ladda upp dokumenthistorik](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Visa uppladdningshistorik

På sidan ladda upp historik kan du visa historik över alla dokumentuppladdningar som dokumenttyp, språkpar, uppladdningsstatus etc.

1. Klicka på Fliken Ladda upp historik på portalen [Anpassad översättare](https://portal.customtranslator.azure.ai) för att visa historik.

    ![Fliken Ladda upp historik](media/how-to/how-to-upload-history-1.png)

2. På den här sidan visas status för alla dina tidigare uppladdningar. Den visar uppladdningar från senaste till minst senaste. För varje uppladdning visas dokumentets namn, uppladdningsstatus, uppladdningsdatum, antalet uppladdade filer, typ av uppladdad fil och filens språkpar.

    ![Sidan Ladda upp historik](media/how-to/how-to-document-history-2.png)

3. Klicka på någon uppladdningshistorikpost. På sidan information om uppladdningshistorik kan du visa de filer som laddats upp som en del av uppladdningen, uppladdad status för filen, filens språk och felmeddelandet (om det finns något fel i överföringen).

## <a name="next-steps"></a>Nästa steg

- Använd [sidan dokumentinformation](how-to-view-document-details.md) för att granska listan över extraherade meningar.
- [Hur man tränar en modell](how-to-train-model.md).
