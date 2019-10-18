---
title: Så här gör du en offline-utvärdering – Personanpassare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du analyserar din inlärnings slinga med en offline-utvärdering
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: diberry
ms.openlocfilehash: bd57880b11f56b13b4225652071593d29dcc6280
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515223"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analysera din inlärnings slinga med en offline-utvärdering

Lär dig hur du slutför en offline-utvärdering och förstår resultatet.

Med offline-utvärdering kan du mäta hur effektiva anpassningar som jämförs med programmets standard beteende, se vilka funktioner som bidrar mest till anpassning och identifiera nya inställningar för maskin inlärning automatiskt.

Läs om [offline-utvärderingar](concepts-offline-evaluation.md) för mer information.


## <a name="prerequisites"></a>Krav

* En konfigurerad personanpassa slinga
* Den personliga slingan måste ha en representativ mängd data – som en ungefärligt rekommenderar vi minst 50 000 händelser i sina loggar för meningsfulla utvärderings resultat. Om du vill kan du också ha tidigare exporterade _Learning-principfiler_ som du kan jämföra och testa i samma utvärdering.

## <a name="steps-to-start-a-new-offline-evaluation"></a>Steg för att starta en ny offline-utvärdering

1. Leta upp din anpassnings resurs i [Azure Portal](https://azure.microsoft.com/free/).
1. I Azure Portal går du till avsnittet **utvärderingar** och väljer **Skapa utvärdering**.
    ![In Azure Portal går du till avsnittet * * utvärdering * * och väljer * * Skapa utvärdering * *. ](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Välj följande inställningar:

    * Ett utvärderings namn
    * Start-och slutdatum – dessa datum är tidigare, vilket anger det data intervall som ska användas i utvärderingen. Dessa data måste finnas i loggarna som anges i inställningen [datakvarhållning](how-to-settings.md) .
    * Optimerings identifiering har angetts till **Ja**

    ![Välj inställningar för utvärdering av offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Starta utvärderingen genom att välja **OK**. 

## <a name="results"></a>Resultat

Utvärderingen kan ta lång tid att köra, beroende på mängden data som ska bearbetas, antalet inlärnings principer som ska jämföras och om en optimering har begärts.

När du är klar kan du välja utvärderingen i listan över utvärderingar. 

Jämförelser av inlärnings principer är:

* **Online princip**: den aktuella inlärnings principen som används i personanpassa
* **Bas linje**: programmets standard (som fastställs av den första åtgärden som skickas i rang anrop).
* **Slumpmässig princip**: ett tänkt ranknings beteende som alltid returnerar slumpmässiga val av åtgärder från de angivna.
* **Anpassade principer**: ytterligare inlärnings principer laddas upp när utvärderingen startades.
* **Optimerad princip**: om utvärderingen startades med alternativet för att identifiera en optimerad princip kommer den också att jämföras och du kommer att kunna ladda ned den eller göra den till en utbildnings princip som ersätter den aktuella.

![Resultat diagram över inställningar för offline-utvärdering](./media/offline-evaluation/evaluation-results.png)

Effektiviteten i [funktioner](concepts-features.md) för åtgärder och sammanhang.

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur offline-utvärdering fungerar](concepts-offline-evaluation.md).
