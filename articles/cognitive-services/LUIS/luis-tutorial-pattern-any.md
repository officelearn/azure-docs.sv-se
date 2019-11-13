---
title: 'Självstudie: mönster. alla entiteter – LUIS'
titleSuffix: Azure Cognitive Services
description: Använd entiteten pattern.any för att extrahera data från yttranden som är välformade och där dataslutet lätt kan förväxlas med återstående ord i yttrandet.
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: c118a74e273e153746240465692b1c712bd25f47
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953609"
---
# <a name="tutorial-extract-free-form-data-with-patternany-entity"></a>Självstudie: extrahera fri Forms data med mönster. alla entiteter

I den här självstudien ska du använda entiteten pattern.any för att extrahera data från yttranden som är välformade och där dataslutet lätt kan förväxlas med återstående ord i yttrandet. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Importera exempelappen
> * Lägga till exempel på yttranden i en befintlig entitet
> * Skapa entiteten Pattern.any
> * Skapa mönster
> * Träna
> * Testa nytt mönster

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="using-patternany-entity"></a>Med Pattern.any-entitet

Med entiteten pattern.any kan du söka efter friformsdata där formuleringen i entiteten gör det svårt att fastställa var entiteten slutar baserat på resten av yttrandet. 

Den här personalappen hjälper anställda att hitta företagsformulär. 

|Yttrande|
|--|
|Var finns **HRF-123456**?|
|Vem skapade **HRF 123234**?|
|Har **HRF 456098** publicerats på franska?|

Varje formulär har dock både ett formaterat namn, som används i tabellen ovan, samt ett eget namn, till exempel `Request relocation from employee new to the company 2018 version 5`. 

Yttranden med det egna formulärnamnet ser ut så här:

|Yttrande|
|--|
|Var finns **Begär flytt från medarbetare som är ny i företaget 2018 version 5**?|
|Vem skapade **”Begär flytt från medarbetare som är ny i företaget 2018 version 5”** ?|
|Har **Begära flytt från medarbetare som är ny i företagets 2018 version 5** publicerats på franska?|

Den varierande längden innehåller ord som kan förvirra LUIS om var entiteten slutar. Genom att använda en Pattern.any-entitet i ett mönster kan du ange formulärnamnets början och slut, så att LUIS kan extrahera formulärnamnet korrekt.

|Exempelmall för yttranden|
|--|
|Var finns {FormName}[?]|
|Vem skapade {FormName}[?]|
|Har {FormName} publicerats på franska[?]|

## <a name="import-example-app"></a>Importera exempelappen

1. Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-pattern-roles-HumanResources.json).

1. På sidan **Mina appar** i [Luis-portalen](https://www.luis.ai)importerar du JSON till en ny app.

1. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `patt-any`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser.

## <a name="add-example-utterances"></a>Lägga till exempelyttranden 

1. Välj **Skapa** från det övre navigeringsfältet, och välj sedan **Avsikter** i det vänstra navigeringsfältet.

1. Välj **FindForm** från listan över avsikter.

1. Lägg till några exempel på yttranden:

    |Exempeluttryck|
    |--|
    |Var finns formuläret **Vad gör du vid eldsvåda i laboratoriet** och vem behöver signera det när jag har läst det?|
    |Var finns **Begär flytt från medarbetare som är ny i företaget** på servern?|
    |Vem skapade "**Hälsofrågor på huvudcampus**" och vilken version är mest aktuell?|
    |Jag letar efter formuläret med namnet "**Begäran om kontorsflytt inklusive fysiska tillgångar**". |

    Utan en Pattern.any-entitet skulle det vara svårt för LUIS för att förstå var formulärrubriken slutar på grund av de många varianterna på formulärnamn.

## <a name="create-a-patternany-entity"></a>Skapa en Pattern.any-entitet
Entiteten Pattern.any extraherar entiteter med olika längd. Det fungerar bara i ett mönster, eftersom mönstret markerar början och slutet av entiteten.  

1. Välj **Entiteter** i det vänstra navigeringsfältet.

1. Välj **Skapa ny entitet**, ange namnet `FormName` och välj **Pattern.any** som typ. Välj **Done** (Klar). 

    Du kan inte märka entiteten i ett yttranden-exempel eftersom det finns ett mönster. any är bara giltig i ett mönster. 

    Om du vill att extraherade data även ska omfatta andra entiteter, som antal eller datetimeV2, måste du skapa en sammansatt entitet som innehåller Pattern.any, antalet och datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **Mönster** i det vänstra navigeringsfönstret.

1. Välj avsikten **FindForm**.

1. Ange följande mallyttranden, som använder den nya entiteten:

    |Mallyttranden|
    |--|
    |Var finns formuläret ["]{FormName}["] och vem behöver signera det när jag har läst det[?]|
    |Var finns ["]{FormName}["] på servern[?]|
    |Vem skapade ["]{FormName}["] och vilken version är mest aktuell[?]|
    |Jag letar efter formuläret med namnet ["]{FormName}["][.]|

    Om du vill ta hänsyn till variationer i formuläret, till exempel enkla citattecken i stället för dubbla eller en punkt i stället för ett frågetecken, skapar du ett nytt mönster för varje variant.

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av friformsdata
1. Välj **Testa** från det översta fältet för att öppna testpanelen. 

1. Fyll i följande yttrande: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Välj **Granska** under resultatet för att se testresultaten för entitet och avsikt.

    Entiteten `FormName` hittas först och sedan mönstret, som fastställer avsikten. Om du har ett testresultat där entiteterna inte identifieras och mönstret därmed inte hittas, behöver du lägga till fler exempelyttranden för avsikten (inte mönstret).

1. Stäng testpanelen genom att välja knappen **Testa** i det övre navigeringsfältet.

## <a name="using-an-explicit-list"></a>Använda en explicit lista

Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Den här självstudien har lagt till exempelyttranden i en befintlig avsikt och därefter skapat en ny Pattern.any-entitet för formulärnamnet. Självstudien skapade sedan ett mönster för den befintliga avsikten med de nya exempelyttrandena och entiteten. Interaktiv testning visade att mönstret och avsikten var förutsagda eftersom entiteten hittades. 

> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern-roles.md)
