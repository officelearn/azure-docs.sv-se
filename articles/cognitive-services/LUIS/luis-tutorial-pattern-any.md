---
title: 'Självstudiekurs 5: Pattern.any entitet för Fritextfält'
titleSuffix: Azure Cognitive Services
description: Använd entiteten pattern.any för att extrahera data från yttranden där talade är välutformat och där slutet av data kan förväxlas enkelt med återstående ord av uttryck.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: a5b964877f1c089015812abdf365b22c57267bb0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031382"
---
# <a name="tutorial-5-extract-free-form-data"></a>Självstudie: 5. Extrahera friformsdata

I den här självstudien använder du entiteten pattern.any för att extrahera data från yttranden där talade är välutformat och där slutet av data kan förväxlas enkelt med återstående ord av uttryck. 

Entiteten pattern.any kan du söka efter fritt format data där formulering entitetens gör det svårt att fastställa slutet av entiteten från resten av uttryck. 

Den här personalapp hjälper anställda att hitta företagets formulär. 

|Yttrande|
|--|
|Där är **HRF 123456**?|
|Vem som skapade **HRF 123234**?|
|**HRF 456098** publiceras på franska?|

Varje formulär har dock både en formaterad namn, används i tabellen ovan, samt ett eget namn, till exempel `Request relocation from employee new to the company 2018 version 5`. 

Yttranden med egna formulärnamnet se ut:

|Yttrande|
|--|
|Där är **begära flytt från medarbetare som är ny i företagets 2018 version 5**?|
|Vem som skapade **”begär flytt från medarbetare som är ny i företagets 2018 version 5”**?|
|**Begära flytt från medarbetare som är ny i företagets 2018 version 5** publiceras på franska?|

Varierande längd innehåller ord som kan förvirra LUIS om var entiteten slutar. Med hjälp av en Pattern.any entitet i arbetsprofilen kan du ange början och slutet av formulärnamnet så LUIS korrekt extraherar formulärnamnet.

|Exempelmall för uttryck|
|--|
|Där är {FormName} [?]|
|Som skapats {FormName} [?]|
|{FormName} har publicerats på franska [?]|

**I den här självstudien får du lära dig hur du:**

> [!div class="checklist"]
> * Använd befintliga självstudieappen
> * Lägg till exempel yttranden till befintlig entitet
> * Skapa Pattern.any entitet
> * Skapa mönster
> * Träna
> * Nya mönster

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använd befintlig app
Fortsätt med den app som skapats i den sista självstudien med namnet **ska**. 

Om du inte har appen ska från den tidigare självstudiekursen, använder du följande steg:

1.  Hämta och spara [app JSON-fil](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-roles-HumanResources.json).

2. Importera JSON till en ny app.

3. Från den **hantera** avsnittet på den **versioner** fliken klona versionen och ge den namnet `patt-any`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används som en del av URL: en väg, får inte namnet innehålla några tecken som inte är giltiga i en URL.

## <a name="add-example-utterances"></a>Lägg till exempel yttranden 
Ta bort entiteten fördefinierade keyPhrase om det är svårt att skapa och kategorisera FormName entiteten. 

1. Välj **skapa** från det övre navigeringsfältet väljer **avsikter** i vänstra navigeringsfönstret.

2. Välj **FindForm** från listan över avsikter.

3. Lägg till yttranden några exempel:

    |Exempel-uttryck|
    |--|
    |Där är formuläret **vad du gör när fire delar upp i laboratoriet** och vilka som behöver att signera den när jag har läst?|
    |Där är **begära flytt från medarbetare som är nytt för företaget** på servern?|
    |Vem som har skapats ”**hälso- och webbplatsen begäranden på den huvudsakliga campus**” och vad som är den senaste versionen?|
    |Jag letar efter formuläret med namnet ”**Office flyttförfrågan inklusive fysiska tillgångar**”. |

    Utan en Pattern.any entitet, skulle det vara svårt för LUIS för att förstå var formulärrubrik slutar på grund av många variationer av skapa namn.

## <a name="create-a-patternany-entity"></a>Skapa en Pattern.any entitet
Entiteten Pattern.any extraherar entiteter med olika längd. Det fungerar bara i ett mönster eftersom mönstret som markerar början och slutet av entiteten. Om du tycker att din mönstret när det innehåller en Pattern.any, extraherar entiteterna felaktigt, används en [explicit lista](luis-concept-patterns.md#explicit-lists) att lösa problemet. 

1. Välj **entiteter** i det vänstra navigeringsfönstret.

2. Välj **Skapa ny entitet**, anger du namnet `FormName`, och välj **Pattern.any** som typ av. Välj **Done** (Klar). 

    Du kan märka entiteten i avsikten eftersom en Pattern.any är endast giltig i ett mönster. 

    Om du vill extrahera data att inkludera andra entiteter som tal eller datetimeV2, måste du skapa en sammansatt entitet som innehåller Pattern.any, samt antalet och datetimeV2.

## <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **mönster** i det vänstra navigeringsfönstret.

2. Välj den **FindForm** avsikt.

3. Ange följande uttryck från mallen, som använder den nya entiteten:

    |Mallen yttranden|
    |--|
    |Där är form [”] {FormName} [”] och vilka som behöver att signera den när jag har läst den [?]|
    |Där är [”] {FormName} [”] på servern [?]|
    |Vem som har skapats [”] {FormName} [”] och vad som är den mest aktuella versionen [?]|
    |Jag letar efter formuläret med namnet [”] {FormName} [”] [.]|

    Om du vill att kompensera för variationer av formuläret, till exempel enkla citattecken i stället för dubbla citattecken eller en punkt i stället för ett frågetecken, skapa ett nytt mönster för varje variant.

4. Om du har tagit bort entiteten keyPhrase lägga tillbaka det i appen. 

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av data för fritt format
1. Välj **testa** från det översta fältet för att öppna panelen test. 

2. Ange följande uttryck: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Välj **granska** under resultat för att se testresultaten för entitet och avsikt.

    Entiteten `FormName` hittas först och sedan mönstret påträffas, fastställa syftet. Om du har ett testresultat där entiteterna identifieras inte och därför hittas inte mönstret som du behöver lägga till fler exempel yttranden på avsikten (inte mönstret).

4. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Den här självstudien exempel yttranden till en befintlig avsikt och därefter skapas en ny Pattern.any för formulärnamnet. Självstudien skapas ett mönster för befintliga syftet med den nya exempel yttranden och entiteten. Interaktiv testning visade att mönstret och avsikten var förutse eftersom entiteten hittades. 

> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern-roles.md)