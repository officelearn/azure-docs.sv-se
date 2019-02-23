---
title: Hur du överför ett dokument - anpassad Translator
titleSuffix: Azure Cognitive Services
description: Du kan ladda upp parallella dokument för dina utbildningar med hjälp av funktionen för överföring. Parallell dokument är par i dokument om en sådan översättningen av den andra. Ett dokument i paret innehåller meningar i källspråket och det andra dokumentet innehåller dessa meningar översättas till mål-språk.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 83d6791740cd6aebb9da14b86e0d5272ab78f43a
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737594"
---
# <a name="upload-a-document"></a>Ladda upp ett dokument

I [anpassad Translator](https://portal.customtranslator.azure.ai), du kan ladda upp parallella dokument för att träna din översättningsmodeller. [Parallell dokument](what-are-parallel-documents.md) är par av dokument där en är en översättning av de andra. Ett dokument i paret innehåller meningar i källspråket och det andra dokumentet innehåller dessa meningar översättas till mål-språk.

Innan du laddar upp dokument, granska de [dokumentera format och naming convention vägledning](document-formats-naming-convention.md) för att kontrollera att filen formatet stöds i anpassade Translator.

## <a name="how-to-upload-document"></a>Hur du överför dokument?

Från [anpassad Translator](https://portal.customtranslator.azure.ai) portal, klicka på ”dokument” flik för att gå till dokumentsidan.

![Länk till dokumentet uppladdning](media/how-to/how-to-upload-1.png)


1.  Klicka på knappen ladda upp filer på dokumentsidan.

    ![Ladda upp dokumentsida](media/how-to/how-to-upload-2.png)

2.  I dialogrutan fyller du i följande information:

    a.  Typ av standarddokument:

    -  Träning: Dessa dokument ska användas för träningsmängden.
    -  Justering: Dessa dokument används för justering set.
    -  Testning: Dessa dokument används för att testa set.
    -  Fras ordlista: Dessa dokument ska användas för frasen ordlista.
    -  Ordlista för meningen: Dessa dokument som ska användas för meningen ordlista

    b.  Språk-par

    c.  Åsidosätta dokumentet om finns: Välj den här kryssrutan om du vill skriva över eventuella befintliga dokument med samma namn.

    d.  Fyll i relevanta avsnitt för parallella eller kombinationsruta för data.

    -  Parallell data:
        -  Källfil: Välj källspråk fil från din lokala dator.
        -  Målfilen: Välj målspråk fil från din lokala dator.
        -  Dokumentnamn: Används endast om du laddar upp parallella filer.

    - Kombinationsruta för data:
        -  Kombinationsruta för filen: Markera den kombinationsruta-fil från din lokala dator. Kombinationsruta för filen har både för källan och målspråk meningar. [Naming convention](document-formats-naming-convention.md) är viktigt för kombinationsruta för filer.

    e.  Klicka på ladda upp

    ![Ladda upp dokument dialogrutan](media/how-to/how-to-upload-dialog.png)

3.  Nu kan vi bearbeta dina dokument och skulle extraheras meningar. Du kan klicka på ”Visa uppladdningsförlopp” att kontrollera status för dina dokument som de bearbetar.

    ![Ladda upp dokument bearbetning dialogrutan](media/how-to/how-to-upload-processing-dialog.png)

4.  Den här sidan visar status och eventuella fel för varje fil i ladda upp. Du kan visa tidigare Överföringsstatusen när som helst genom att klicka på fliken ”ladda upp historik”.

    ![Ladda upp dokument historik dialogrutan](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Visa historik för uppladdning

I uppladdningssidan historik som du kan visa historiken för alla dokument uppladdningar information som typ av standarddokument, språk-par, för att överföra status osv.

1. Från den [anpassad Translator](https://portal.customtranslator.azure.ai) portal, ladda upp historik fliken om du vill visa historik.

    ![Ladda upp fliken Historik](media/how-to/how-to-upload-history-1.png)

2. Den här sidan visar status för alla dina senaste uppladdningar. Den visar uppladdningar från det senaste till det tidigaste. För varje överföring visas dokumentnamnet, Överföringsstatusen, ladda upp datumet, antalet filer som överförts, typ av fil har laddats upp och språk-par med filen.

    ![Ladda upp sidan tidigare](media/how-to/how-to-document-history-2.png)

3. Klicka på en post, ladda upp historik. Du kan visa de filer som laddas upp som en del av överföringen, överförda status för filen, språket i fil- och fel meddelandet (om det finns några fel i överföringen) överför historik information på sidan.

## <a name="next-steps"></a>Nästa steg

- Använd den [dokumentet informationssidan](how-to-view-document-details.md) att granska listan över extraherade meningar.
- [Hur du tränar en modell](how-to-train-model.md).
