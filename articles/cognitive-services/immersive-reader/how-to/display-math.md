---
title: Visa matematik i den uppslukande läsaren
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du visar matematik i Immersive Reader.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946126"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Så här visar du matematik i den uppslukande läsaren

Den uppslukande läsaren kan visa matematik när den tillhandahålls i form av matematiskt markeringsspråk ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
MIME-typen kan ställas in via [segmentet](../reference.md#chunk)Immersive Reader . Mer information [finns i MIME-typer som stöds.](../reference.md#supported-mime-types)

## <a name="send-math-to-the-immersive-reader"></a>Skicka matematik till den uppslukande läsaren
För att skicka matematik till Immersive Reader, ange ett segment som innehåller ```application/mathml+xml```MathML och ställa in MIME-typen till ;

Om ditt innehåll till exempel var följande:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Då kan du visa ditt innehåll med hjälp av följande JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

När du startar Immersive Reader bör du se:

![Matematik i uppslukande läsare](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](../reference.md)