---
title: Självstudier med mönster för att förbättra LUIS förutsägelser – Azure | Microsoft Docs
titleSuffix: Azure
description: I den här självstudien använder du mönstret för avsikter för att förbättra LUIS avsikt och entiteten förutsägelser.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 12105829f62b988760d3bbf18000466fd27b9aff
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888340"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Självstudier: Använda mönster för att förbättra förutsägelser

Använda mönster i de här självstudierna för att öka avsikt och entiteten förutsägelse.  

> [!div class="checklist"]
* Så här identifierar du att ett mönster vill hjälpa din app
* Så här skapar du ett mönster 
* Hur du använder fördefinierade och anpassade entiteter i ett mönster 
* Så här verifierar du mönstret förutsägelse förbättringar
* Hur du lägger till en roll till en entitet att hitta sammanhangsmässigt-baserade enheter
* Hur du lägger till en Pattern.any för att hitta fri form entiteter

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md) för att kunna redigera LUIS-programmet.

## <a name="import-humanresources-app"></a>Importera ska appen
Den här självstudien importerar en app ska. Appen har tre avsikter: Ingen, GetEmployeeOrgChart, GetEmployeeBenefits. Appen har två entiteter: fördefinierade tal och medarbetare. Medarbetaren entiteten är en enkel enhet att extrahera en medarbetares namn. 

1. Skapa en ny LUIS-app-fil och ge den namnet `HumanResources.json`. 

2. Kopiera följande app-definition i filen:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. På LUIS **appar** väljer **importera ny app**. 

4. I den **importera ny app** dialogrutan den `HumanResources.json` fil som du skapade i steg 1.

5. Välj den **GetEmployeeOrgChart** avsikt, och sedan ändra från **entiteter visa** till **Tokens visa**. Flera exempel yttranden visas. Varje uttryck innehåller ett namn som är en anställd entitet. Observera att namnen är olika och att ordningen för texten är olika för varje uttryck. Den här mångfald hjälper LUIS Lär dig en mängd olika yttranden.

    ![Skärmbild av avsikt sida med entiteter visa växlas](media/luis-tutorial-pattern/utterances-token-view.png)

6. Välj **träna** i det övre navigeringsfältet för att träna appen. Vänta tills fältet grönt.

7. Välj **Test** i den övre panelen. Ange `Who does Patti Owens report to?` och sedan anger du väljer. Välj **granska** under uttryck att se mer information om testet.
    
    Medarbetarnamn, Patti Owens har inte använts i en exempel-uttryck ännu. Det här är ett test för att se hur väl LUIS lärt dig den här uttryck avser den `GetEmployeeOrgChart` avsikt och entiteten anställda ska vara `Patti Owens`. Resultatet bör vara mindre än 50% (. 50) för den `GetEmployeeOrgChart` avsikt. Avsikten är rätt, är att resultatet låg. Den anställdas också korrekt identifieras som `Patti Owens`. Mönster öka det här första förutsägelse resultatet. 

    ![Skärmbild av Test-panelen](media/luis-tutorial-pattern/original-test.png)

8. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Mönster Lär LUIS vanliga yttranden med färre exempel
På grund av mänskliga resursdomänen finns det några vanliga sätt att be om medarbetaren relationer i organisationer. Exempel:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Dessa uttryck är för Stäng om du vill fastställa sammanhangsberoende unikhet för var och en utan att ange många uttryck exemplen. Lär sig vanliga mönster för uttryck för en avsikt utan att ange många uttryck exempel genom att lägga till ett mönster för en avsikt, LUIS. 

Exempel mallen yttranden avsikt följande:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Mönstret är en kombination av matchning med reguljära uttryck och machine learning. Ange därefter vissa mall uttryck exempel för LUIS för att lära dig mönstret. De här exemplen, tillsammans med avsikt yttranden ger LUIS bättre förståelse för vilka yttranden passar avsikten och där, i uttryck, entiteten finns. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Lägg till mall-yttranden

1. I det vänstra navigeringsfönstret under **förbättra apprestanda**väljer **mönster** i det vänstra navigeringsfönstret.

2. Välj den **GetEmployeeOrgChart** avsikt, ange följande uttryck från mall, en i taget, att välja returtangenten efter varje mall-uttryck:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    Den `{Employee}` syntax markerar entitet plats i mallen-uttryck som samt vilken entitet som det är. 

    ![Skärmbild av hur du anger mallen yttranden för avsikt](./media/luis-tutorial-pattern/enter-pattern.png)

3. Välj **träna** i det övre navigeringsfältet. Vänta tills fältet grönt.

4. Välj **Test** i den övre panelen. Ange `Who does Patti Owens report to?` i textrutan. Välj ange. Det här är samma uttryck testas i föregående avsnitt. Resultatet bör vara högre för den `GetEmployeeOrgChart` avsikt. 

    Poängen är nu mycket bättre. LUIS lärt dig mönstret rör avsikten utan att ange många exemplen.

    ![Skärmbild av Test panelen Poängrekord resultera](./media/luis-tutorial-pattern/high-score.png)

    Entiteten finns först och sedan mönstret påträffas, som anger syftet. Om du har ett testresultat där entiteten identifieras inte och därför hittas inte mönstret som du behöver lägga till fler exempel yttranden på avsikten (inte mönstret). 

5. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Använda en entitet med en roll i ett mönster
LUIS-appen används för att flytta anställda från en plats till en annan. En exempel-uttryck är `Move Bob Jones from Seattle to Los Colinas`. Varje plats i uttryck har en annan betydelse. Seattle är den ursprungliga platsen och Los Colinas är målplatsen för flytten. För att skilja mellan dessa platser i mönstret i följande avsnitt du skapar en enkel enhet för platsen med två roller: original och beskrivning. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Skapa en ny avsikt för att flytta personer och tillgångar
Skapa ett nytt syfte med yttranden som är i färd glidande personer eller tillgångar.

1. Välj **avsikter** i vänstra navigeringsfönstret
2. Välj **skapa nya avsikt**
3. Namn på ny avsikten `MoveAssetsOrPeople`
4. Lägg till exempel yttranden:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Skärmbild av exempel uttryck för MoveAssetsOrPeople avsikt](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Syftet med exempel yttranden är att ge tillräckligt med exempel. Om du senare i testet, plats entiteten är inte identifierats och därför mönstret är inte har identifierats, gå tillbaka till det här steget och lägga till fler exempel. Sedan träna och testa igen. 

5. Markera entiteter i exempel-uttryck med entitet som anställd genom att välja förnamn sedan efternamn i ett uttryck och välja entiteten medarbetare i listan.

    ![Skärmbild av yttranden i MoveAssetsOrPeople som markerats med medarbetare entitet](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Markera texten `portland` i uttryck `move travis hinton from portland to orlando`. Ange det nya namnet i standardarbetsytan i dialogrutan `Location`, och välj **Skapa ny entitet**. Välj den **enkel** entitetstypen och välj **klar**.

    ![Skärmbild av den nya platsen entitet skapas](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Markera resten av platsnamn i talade. 

    ![Skärmbild av alla enheter som har markerats](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Mönstret för val av word och ordning är uppenbart i föregående bild. Om du skulle **inte** med hjälp av mönster och yttranden på avsikten har ett uppenbart mönster som är en bra indikation på bör du använda mönster. 

    Om du förväntar dig en mängd olika yttranden, i stället för ett mönster, skulle dessa vara fel exempel yttranden. I så fall skulle du vill ha många olika yttranden i termen eller word val, uttryck längd och placering för entiteten. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Lägg till plats entitet 
Roller kan bara användas för mönster. Lägg till rollerna för original och beskrivning i plats-entiteten. 

1. Välj **entiteter** i det vänstra navigeringsfönstret, sedan **plats** från listan över entiteter.

2. Lägg till `Origin` och `Destination` roller till entiteten.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/location-entity.png)

    Rollerna som inte har markerats på sidan MoveAssetsOrPeople avsikt eftersom roller som inte finns på avsikt yttranden. De finns bara på mönstret mall yttranden. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Lägg till mall yttranden som använder plats-och mål
Lägg till mall yttranden som använder den nya entiteten.

1. Välj **mönster** i det vänstra navigeringsfönstret.

2. Välj den **MoveAssetsOrPeople** avsikt.

3. Ange en ny mall-uttryck med hjälp av den nya entiteten `Move {Employee} from {Location:Origin} to {Location:Destination}`. Syntaxen för en enhet och en roll i en mall-uttryck är `{entity:role}`.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Träna appen för den nya avsikt, entitet och mönster.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testa det nya mönstret för rollen extrahering av data
Verifiera det nya mönstret med ett test.

1. Välj **Test** i den övre panelen. 
2. Ange uttryck `Move Tammi Carlson from Bellingham to Winthrop`.
3. Välj **granska** under resultat för att se testresultaten för entitet och avsikt.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/test-with-roles.png)

    Entiteterna finns först och sedan mönstret påträffas, som anger syftet. Om du har ett testresultat där entiteterna identifieras inte och därför hittas inte mönstret som du behöver lägga till fler exempel yttranden på avsikten (inte mönstret). 

4. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Använd en Pattern.any entitet för att hitta fri form entiteter i ett mönster
Den här appen ska hjälper också att anställda hitta företagets formulär. Många av formulär har som varierande längd. Varierande längd innehåller fraser som kan förvirra LUIS om var formulärnamnet slutar. Med hjälp av en **Pattern.any** entitet i arbetsprofilen kan du ange början och slutet av formulärnamnet så LUIS korrekt extraherar formulärnamnet. 

### <a name="create-a-new-intent-for-the-form"></a>Skapa en ny avsikt för formuläret
Skapa ett nytt syfte med yttranden som söker efter formulär.

1. Välj **avsikter** i vänstra navigeringsfönstret.

2. Välj **Create new intent** (Skapa ny avsikt).

3. Namnge den nya avsikten `FindForm`.

4. Lägg till en exempel-uttryck.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/intent-findform.png)

    Formulärets rubrik är `What to do when a fire breaks out in the Lab`. Uttryck frågar efter plats i formatet och även ber som behöver för att registrera den verifierar medarbetaren läsa den. Utan en Pattern.any entitet, skulle det vara svårt att förstå var formulärrubrik slutar och extrahera rubriken som en enhet med uttryck.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Skapa en Pattern.any entitet för formulärets rubrik
Entiteten Pattern.any möjliggör entiteter med olika längd. Det fungerar bara i ett mönster eftersom mönstret som markerar början och slutet av entiteten. Om du tycker att din mönstret när det innehåller en Pattern.any, extraherar entiteterna felaktigt, används en [explicit lista](luis-concept-patterns.md#explicit-lists) att lösa problemet. 

1. Välj **entiteter** i det vänstra navigeringsfönstret.

2. Välj **Create new entity** (Skapa ny entitet). 

3. Namnge entiteten `FormName` med typen **Pattern.any**. Den här specifika självstudien behöver inte lägga till några roller i entiteten.

    ![Bild av dialogrutan för enhetens namn och typ av enhet](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **mönster** i det vänstra navigeringsfönstret.

2. Välj den **FindForm** avsikt.

3. Ange ett mall-uttryck med hjälp av den nya entiteten `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Skärmbild av mallen uttryck pattern.any entitet](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Träna appen för den nya avsikt, entitet och mönster.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av data för fritt format
1. Välj **testa** från det översta fältet för att öppna panelen test. 

2. Ange uttryck `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Välj **granska** under resultat för att se testresultaten för entitet och avsikt.

    ![Skärmbild av mallen uttryck pattern.any entitet](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Entiteten finns först och sedan mönstret påträffas, som anger syftet. Om du har ett testresultat där entiteterna identifieras inte och därför hittas inte mönstret som du behöver lägga till fler exempel yttranden på avsikten (inte mönstret).

4. Stänga panelen test genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Om du vill göra det, Välj ellipsen (***...*** ) till höger om appnamnet i programlistan, Välj **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs om bästa praxis för LUIS-appar](luis-concept-best-practices.md)