---
title: Hierarkisk entitet
titleSuffix: Azure Cognitive Services
description: Hitta relaterade datadelar baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: diberry
ms.openlocfilehash: a79c0091220e2980101471abaaa0aaf4c0a898ca
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104415"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Självstudie 5: Extrahera sammanhangsbaserade data
I den här självstudien hittar du relaterade datadelar baserat på kontext. Till exempel är ett ursprung och målplatser för en fysisk flytt från en byggnad och ett kontor till en annan byggnad och ett annat kontor relaterade. För att generera en arbetsorder kan båda datadelarna krävas, och de är relaterade till varandra.  

Den här appen bestämmer var en medarbetare ska flyttas från (byggnad och kontor) och till (byggnad och kontor). Den använder den hierarkiska entiteten för att urskilja platser i yttrandet. Syftet med den **hierarkiska** entiteten är att hitta relaterade data inom yttrandet baserat på kontext. 

Den hierarkiska entiteten passar bra för den här typen av data eftersom följande gäller för de två datadelarna:

* De är enkla entiteter.
* De är relaterade till varandra i yttrandet.
* Specifika ord används för att ange varje plats. Exempel på sådana ord: från/till, lämnar/ska till, bort från/till.
* Båda platserna förekommer ofta i samma yttrande. 
* Båda måste grupperas och bearbetas av klientappen som en informationsenhet.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Använda en befintlig självstudieapp
> * Lägga till avsikt 
> * Lägg till platshierarkisk entitet med ursprung och underordnade destinationselement
> * Träna
> * Publicera
> * Hämta avsikter och entiteter från en slutpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Använda en befintlig app
Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Om du inte har appen HumanResources från föregående självstudie gör du så här:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `hier`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Ta bort fördefinierad nummerentitet från appen
Om du vill se hela yttrandet och märka de underordnade delarna i hierarkin kan du [ta bort den fördefinierade nummerentiteten temporärt](luis-prebuilt-entities.md#marking-entities-containing-a-prebuilt-entity-token). 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Välj **Entities** (Entiteter) på den vänstra menyn.

3. Markera kryssrutan till vänster om nummerentiteten i listan. Välj **Ta bort**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Lägga till yttranden till avsikten MoveEmployee

1. Välj **Intents** (Avsikter) på den vänstra menyn.

2. Välj **MoveEmployee** i listan med avsikter.

3. Lägg till följande exempelyttranden:

    |Exempel på yttranden|
    |--|
    |Flytta John W. Smith **till** a-2345|
    |Hänvisa Jill Jones **till** b-3499|
    |Ordna flytt för x23456 **från** hh-2345 **till** e-0234|
    |Påbörja pappersarbetet för att x12345 **ska flytta från** a-3459 **till** f-34567|
    |Flytta 425-555-0000 **från** g-2323 **till** hh-2345|

    [ ![Skärmbild på LUIS-appen med nya yttranden i avsikten MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    I enlighet med självstudien om [listentitet](luis-quickstart-intent-and-list-entity.md) anges en medarbetare med namn, e-postadress, telefonanknytning, mobilnummer eller amerikanskt socialförsäkringsnummer. Dessa medarbetarnummer används i yttranden. Föregående exempelyttranden innehåller olika sätt att ange ursprungs- och målplatser, markerade i fetstil. I några av yttrandena ingår endast målplatser. Detta hjälper LUIS-appen att förstå hur dessa platser placeras i yttranden när ursprungsplats inte anges.     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Skapa en platsentitet
LUIS-appen behöver förstå vad en plats är genom att märka ursprungs- och målplatser i yttranden. Om du behöver se ett yttrande i tokenvyn (obearbetad) väljer du växlingsknappen i fältet ovanför yttrandena som är märkta **Entities View** (Entitetsvy). När du byter läge är kontrollen märkt **Tokens View** (Tokenvy).

Ta följande yttrande som exempel:

```json
mv Jill Jones from a-2349 to b-1298
```

I yttrandet finns två angivna platser: `a-2349` och `b-1298`. Anta att bokstaven motsvarar ett byggnadsnamn och numret indikerar kontoret i byggnaden. Båda dessa är underordnade den hierarkiska entiteten `Locations` eftersom båda datadelar behöver extraheras från yttrandet för att slutföra begäran i klientprogrammet och de är relaterade till varandra. 
 
Om endast ett underordnat element (ursprung eller destination) för en hierarkisk entitet finns extraheras det ändå. Det är inte nödvändigt att hitta alla underordnade element för att endast ett eller några element ska kunna extraheras. 

1. I yttrandet `Displace 425-555-0000 away from g-2323 toward hh-2345` väljer du ordet `g-2323`. En listrutemeny visas med en textruta längst upp. Ange entitetsnamnet `Locations` i textrutan och välj sedan **Create new entity** (Skapa ny entitet) i listrutan. 

    [![Skärmbild av skapande av ny entitet på avsiktssidan](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Skärmbild av skapande av ny entitet på avsiktssidan")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. I popup-fönstret väljer du entitetstypen **Hierarchical** (Hierarkisk) med `Origin` och `Destination` som underordnade entiteter. Välj **Done** (Klar).

    ![Skärmbild av popup-fönstret för entitetsskapande för den nya platsentiteten](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Skärmbild av popup-fönstret för entitetsskapande för den nya platsentiteten")

3. Etiketten för `g-2323` har märkts som `Locations` eftersom LUIS inte känner till om termen var ursprunget, målet, eller ingetdera. Välj `g-2323`, välj sedan **Locations** (Platser) och följ därefter menyn till höger och välj `Origin`.

    [![Skärmbild av popup-fönstret för entitetsetikettering vid ändring av platsentitets underordnad](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Skärmbild av popup-fönstret för entitetsetikettering vid ändring av platsentitets underordnad")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Märk de andra platserna i alla andra yttranden genom att välja byggnad och kontor i yttrandet, välja Locations (Platser), följa menyn till höger och välja `Origin` eller `Destination`. När alla platser är märkta börjar yttrandena i **Tokens View** (Tokenvy) att få ett mönster. 

    [![Skärmbild av platsentitet etiketterad i yttranden](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Skärmbild av platsentitet etiketterad i yttranden")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Lägga till fördefinierad nummerentitet i appen
Lägg till den fördefinierade nummerentiteten i appen.

1. Välj **Entities** (Entiteter) på den vänstra navigeringsmenyn.

2. Välj knappen för att **lägga till en fördefinierad entitet**.

3. Välj **number** (nummer) i listan över fördefinierade entiteter och sedan **Done** (Klar).

    ![Skärmbild på dialogrutan för fördefinierade entiteter med nummer markerat](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Träna LUIS-appen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publicera appen för att få slutpunkts-URL

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Skicka fråga till slutpunkten med ett annat yttrande

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Gå till slutet av webbadressen i adressfältet och ange `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Den sista frågesträngsparametern är `q`, yttrande**frågan**. Det här yttrandet är inte samma som någon av de märkta yttrandena. Därför är det ett bra test och bör returnera avsikten `MoveEmployee` med den hierarkiska entiteten extraherad.

    ```json
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    Den rätta avsikten förutsägs och entitetsmatrisen har både ursprunget och målvärdena i motsvarande **entitetsegenskap**.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Går det att använda ett reguljärt uttryck för varje plats?
Ja, skapa enheten för reguljära uttryck med ursprungs- och målroller och använd den i ett mönster.

Platserna i det här exemplet, som `a-1234`, har ett visst format med en eller två bokstäver följt av ett bindestreck, och sedan ett nummer med 4–5 tal. Dessa data kan beskrivas som en entitet för reguljära uttryck med en roll för varje plats. Roller är endast tillgängliga för mönster. Du kan skapa mönster baserade på dessa yttranden och sedan skapa ett reguljärt uttryck för platsformatet och lägga till det i mönster. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>Hierarkiska entiteter jämfört med roller

Mer information finns i [Roller jämfört med hierarkiska entiteter](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="next-steps"></a>Nästa steg
Den här självstudien skapade en ny avsikt och lade till exempelyttranden för sammanhangsmässigt inlärda data för ursprungs- och målplatser. När appen har tränats och publicerats kan ett klientprogram använda den informationen för att skapa en flyttningsbiljett med relevant information.

> [!div class="nextstepaction"] 
> [Lär dig hur du lägger till en sammansatt entitet](luis-tutorial-composite-entity.md) 
