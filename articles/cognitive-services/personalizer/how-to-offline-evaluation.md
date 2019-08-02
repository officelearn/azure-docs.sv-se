---
title: Offline-utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du analyserar din inlärnings slinga med en offline-utvärdering
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: f14403422e2c783d75634bb929d8c2130bd505b6
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663883"
---
# <a name="how-to-analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysera din inlärnings slinga med en offline-utvärdering


Lär dig hur du slutför en offline-utvärdering och förstår resultatet.

Med offline-utvärdering kan du mäta hur effektiva anpassningar som jämförs med programmets standard beteende, se vilka funktioner som bidrar mest till anpassning och identifiera nya inställningar för maskin inlärning automatiskt.

Läs om [offline](concepts-offline-evaluation.md) -utvärderingar för mer information.


## <a name="prerequisites"></a>Förutsättningar

1. Du måste ha en egen-loop konfigurerad
1. Den personliga slingan måste ha minst 50 000 händelser i sina loggar för att få meningsfulla utvärderings resultat.

Om du vill kan du också ha tidigare exporterade _Learning-principfiler_ som du kan jämföra och testa i samma utvärdering.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Steg för att starta en ny offline-utvärdering

1. Leta upp resursen anpassnings slinga i Azure Portal.
1. Gå till avsnittet "utvärdering".
1. Klicka på ny utvärdering
1. Välj ett start-och slutdatum för offline-utvärderingen. Detta är datum tidigare, som anger det data intervall som ska användas i utvärderingen. Dessa data måste finnas i loggarna som anges i inställningen datakvarhållning. [](how-to-settings.md)
1. Du kan också ladda upp din egen utbildnings princip. 
1. Ange om Personanpassaren ska skapa en optimerad inlärnings princip baserat på användar beteendet som observerats under den här tids perioden.
1. Starta utvärderingen

## <a name="results"></a>Resultat

Utvärderingen kan ta lång tid att köra, beroende på mängden data som ska bearbetas, antalet inlärnings principer som ska jämföras och om en optimering har begärts.

När du är klar kan du se följande resultat:

1. Jämförelser av inlärnings principer, inklusive:
    * **Online-princip**: Den aktuella inlärnings principen som används i personanpassa
    * **Bas linje**: Programmets standard (som fastställs av den första åtgärden som skickas i rang anrop).
    * **Slumpmässig princip**: Ett tänkt rangordnings beteende som alltid returnerar slumpmässiga val av åtgärder från de angivna.
    * **Anpassade principer**: Ytterligare inlärnings principer laddades upp när utvärderingen startades.
    * **Optimerad princip**: Om utvärderingen startades med alternativet för att identifiera en optimerad princip, kommer den också att jämföras och du kommer att kunna ladda ned den eller göra den till en online Learning-princip som ersätter den aktuella.

1. Effektiviteten i [funktioner](concepts-features.md) för åtgärder och sammanhang.


## <a name="more-information"></a>Mer information

* Lär dig [hur offline-utvärdering fungerar](concepts-offline-evaluation.md).