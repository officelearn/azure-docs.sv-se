---
title: Självstudie med pattern.any entitet för att förbättra LUIS förutsägelser – Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: I den här självstudien använder du entiteten pattern.any för att förbättra LUIS avsikt och entiteten förutsägelser.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: v-geberr;
ms.openlocfilehash: 95c88bf1e452e945948b47fdada5811db6a0e454
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39174616"
---
# <a name="tutorial-improve-app-with-patternany-entity"></a>Självstudie: Förbättra app med pattern.any entitet

I den här självstudien använder du entiteten pattern.any för att öka avsikt och entiteten förutsägelse.  

> [!div class="checklist"]
* Se när och hur du använder pattern.any
* Skapa mönster som använder pattern.any
* Så här verifierar du förutsägelse förbättringar

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har personalapp från den [mönstret roller](luis-tutorial-pattern-roles.md) självstudien [importera](luis-how-to-start-new-app.md#import-new-app) JSON-koden i en ny app i den [LUIS](luis-reference-regions.md#luis-website) webbplats. App att importera finns i den [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-roles-HumanResources.json) GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `patt-any`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="the-purpose-of-patternany"></a>Syftet med pattern.any
Entiteten pattern.any kan du hitta fri formulärdata där formulering entitetens gör det svårt att fastställa slutet av entiteten från resten av uttryck. 

Den här personalapp hjälper anställda att hitta företagets formulär. Formulär har lagts till i den [reguljärt uttryck självstudien](luis-quickstart-intents-regex-entity.md). Formulärnamn från självstudien används ett reguljärt uttryck för att extrahera ett formulärnamn på som var välutformat, till exempel skapa namn i fetstil i tabellen nedan uttryck:

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

Varierande längd innehåller fraser som kan förvirra LUIS om var entiteten slutar. Med hjälp av en Pattern.any entitet i arbetsprofilen kan du ange början och slutet av formulärnamnet så LUIS korrekt extraherar formulärnamnet.

**Medan mönster kan du ge färre exempel yttranden, om entiteterna inte identifieras, matchar inte mönstret.**

## <a name="add-example-utterances-to-the-existing-intent-findform"></a>Lägg till exempel yttranden till befintliga avsikt FindForm 
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

5. Träna appen.


## <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av data för fritt format
1. Välj **testa** från det översta fältet för att öppna panelen test. 

2. Ange följande uttryck: 

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

3. Välj **granska** under resultat för att se testresultaten för entitet och avsikt.

    Entiteten `FormName` hittas först och sedan mönstret påträffas, fastställa syftet. Om du har ett testresultat där entiteterna identifieras inte och därför hittas inte mönstret som du behöver lägga till fler exempel yttranden på avsikten (inte mönstret).

4. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Om du vill göra det, Välj ellipsen (***...*** ) till höger om appnamnet i programlistan, Välj **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du använder roller med ett mönster](luis-tutorial-pattern-roles.md)