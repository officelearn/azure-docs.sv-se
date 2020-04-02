---
title: 'Självstudiekurs: extrahera strukturerade data med maskininlärd entitet - LUIS'
description: Extrahera strukturerade data från ett uttryck med hjälp av den maskininlärda entiteten. Om du vill öka extraktionsnoggrannheten lägger du till delkomponenter med deskriptorer och begränsningar.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545859"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Självstudiekurs: Extrahera strukturerade data från användarutseende med maskininlärda entiteter i Språkförståelse (LUIS)

I den här självstudien extraherar du strukturerade data från ett uttryck med hjälp av den maskininlärda entiteten.

Den maskininlärda entiteten stöder [modelluppdelningskonceptet](luis-concept-model.md#v3-authoring-model-decomposition) genom att tillhandahålla subkomponententiteter med sina deskriptorer och begränsningar.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Importera exempelappen
> * Lägg till maskininlärd entitet
> * Lägg till delkomponent
> * Lägg till subkomponentens deskriptor
> * Lägg till underkomponentens villkor
> * Träna appen
> * Testa appen
> * Publicera app
> * Hämta entitetsprognos från slutpunkten

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Varför använda en maskininlärd entitet?

Den här självstudien lägger till en dator som lärt sig entiteten för att extrahera data från ett uttryck.

Entiteten definierar data som ska extraheras från uttryck. Detta inkluderar att ge data ett namn, en typ (om möjligt), eventuell upplösning av data om det finns tvetydighet och den exakta texten som utgör data.

För att definiera entiteten måste du skapa entiteten och sedan märka texten som representerar entiteten i exempelyttrandena i alla avsikter. Dessa märkta exempel lär LUIS vad entiteten är och var den finns i ett uttryck.

## <a name="entity-decomposability-is-important"></a>Enhetsnedjänlighet är viktigt

Entitetsuppdekompatibilitet är viktigt för både avsiktsförutsägelse och för datautvinning med entiteten.

Börja med en datorinlärd entitet, som är början och den översta nivånheten för datautvinning. Sedan förmultna entiteten i de delar som behövs av klientprogrammet.

Du kanske inte vet hur detaljerad du vill ha din entitet när du börjar appen, men en metod är att börja med en maskininlärd entitet och sedan delas upp med delkomponenter när appen mognar.

I det här skapar du en maskininlärd entitet som representerar en order för en pizzaapp. Ordern bör ha alla delar som är nödvändiga för att fullfil ordern. Till att börja med extraherar entiteten orderrelaterad text, drar ut storlek och kvantitet.

Ett yttrande `Please deliver one large cheese pizza to me` för `one large cheese pizza` bör extrahera som ordning, sedan också extrahera `1` och `large`.

Det finns ytterligare nedbrytning du kan lägga till såsom att skapa delkomponenter för pålägg eller skorpa. Efter den här självstudien bör du känna dig säker `Order` på att lägga till dessa underkomponenter till din befintliga entitet.

## <a name="import-example-json-to-begin-app"></a>Importera exempel .json för att starta appen

1.  Ladda ner och spara [appen JSON-fil](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>Etiketttext som entiteter i exempelyttranden

Om du vill extrahera information om en pizzabeställning `Order` skapar du en enhet på den högsta nivån.

1. På sidan **Avsikter** väljer du **orderpizza-avsikten.**

1. Markera följande uttryck i listan Exempelyttranden.

    |Orderexempel yttrande|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Börja markera precis före den vänstra `pickup` texten i (#1), sedan gå precis utöver `anchovies` den högra mest text, (#2 - detta avslutar märkningsprocessen). En popup-meny visas. I popup-rutan anger du namnet på `Order` entiteten som (#3). Välj `Order - Create new entity` sedan från listan (#4).

    ![Etikett början och på text för fullständig ordning](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > En entitet är inte alltid hela uttrycket. I det här `pickup` specifika fallet anger du hur ordern ska tas emot. Ur ett konceptuellt perspektiv `pickup` bör vara en del av den märkta entiteten för ordern.

1. I rutan **Välj en entitetstyp** väljer du **Lägg till struktur** och väljer sedan **Nästa**. Struktur är nödvändigt att lägga till delkomponenter som storlek och kvantitet.

    ![Lägga till struktur i entiteten](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Lägg till `Size` i rutan Struktur i rutan **Skapa** **en dator som lärt sig entiteten.**
1. Om du vill lägga till `+` en **deskriptor**markerar du området i **deskriptorerna** och väljer sedan **Skapa ny fraslista**.

1. I rutan **Skapa ny fraslista anger** du `SizeDescriptor` namnet och `small`anger `medium`sedan `large`värden för: , och . När rutan **Förslag fylls** i `extra large`markerar `xl`du och . Välj **Klar** om du vill skapa den nya fraslistan.

    Den här fraslistbeskrivningen `Size` hjälper delkomponenten att hitta ord som är relaterade till storlek genom att förse den med exempelord. Den här listan behöver inte innehålla varje storlek ord men bör innehålla ord som förväntas ange storlek.

    ![Skapa en deskriptor för storleksunderkomponenten](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. I fönstret **Skapa en dator som lärt sig entitet** väljer du **Skapa** för att slutföra skapandet av `Size` underkomponenten.

    Entiteten `Order` `Size` med en komponent `Order` skapas men endast entiteten har tillämpats på uttrycket. Du måste märka `Size` entitetstexten i exempelutseendet.

1. I samma exempelutfallet **Size** märker du underkomponenten `large` Storlek genom att markera ordet och sedan väljer entiteten **Storlek** i listrutan.

    ![Märk storlekentiteten för text i uttryck.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    Raden är heldragen under texten eftersom både märkningen och förutsägelsen matchar eftersom du _uttryckligen_ har märkt texten.

1. Märk `Order` entiteten i de återstående yttrandena tillsammans med storlekentiteten. De hakparenteser i `Order` texten anger `Size` den märkta entiteten och entiteten inom.

    |Exempelyttningar för ordning|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Gör entitet och underkomponenter i alla återstående exempelyttranden.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Hur behandlar du underförstådda data som brevet `a` som innebär en enda pizza? Eller bristen `pickup` på `delivery` och för att ange var pizzan förväntas? Eller avsaknaden av en storlek för att ange din standardstorlek på små eller stora? Överväg att behandla implicit datahantering som en del av dina affärsregler i klientprogrammet i stället för eller utöver LUIS.

1. Om du vill träna appen väljer du **Träna**. Utbildningen tillämpar ändringarna, till exempel de nya entiteterna och de märkta yttrandena, på den aktiva modellen.

1. Efter träningen lägger du till ett nytt exempel yttrande till avsikten att se hur väl LUIS förstår den maskininlärda entiteten.

    |Orderexempel yttrande|
    |--|
    |`pickup XL meat lovers pizza`|

    Den övergripande översta `Order` entiteten, `Size` är märkt och underkomponenten är också märkt med prickade linjer.

    ![Nytt exempel yttrande förutspås med entitet](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Den streckade linjen anger förutsägelsen.

1. Om du vill ändra förutsägelsen till en märkt entitet markerar du raden och väljer sedan **Bekräfta entitetsprognoser**.

    ![Acceptera förutsägelse genom att välja Bekräfta entitetsprognos.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    Nu fungerar den datorinlärda entiteten eftersom den kan hitta entiteten i ett nytt exempelutseende. När du lägger till exempelyttranden, om entiteten inte förutses korrekt, märker du entiteten och underkomponenterna. Om entiteten förutses korrekt, se till att bekräfta förutsägelserna.

## <a name="add-prebuilt-number-to-help-extract-data"></a>Lägg till fördefinierat tal för att extrahera data

Orderinformationen ska också innehålla hur många av en vara som är i ordningen, till exempel hur många pizzor. För att extrahera dessa data måste en ny maskininlärd `Order` delkomponent läggas till och komponenten behöver ett villkor för ett fördefinierat tal. Genom att begränsa entiteten till ett fördefinierat tal söker entiteten efter `2`och extraherar tal oavsett om texten är en siffra, eller text. `two`

Börja med att lägga till den fördefinierade nummerentiteten i appen.

1. Välj **Entiteter** på den vänstra menyn och välj sedan **+ Lägg till fördefinierad entitet**.

1. I rutan **Lägg till fördefinierade entiteter** söker du efter och väljer **nummer** och väljer sedan **Klar**.

    ![Lägg till fördefinierad entitet](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    Den fördefinierade entiteten läggs till i appen men är inte en begränsning ännu.

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Skapa underkomponententitet med begränsning för att extrahera data

Entiteten `Order` ska `Quantity` ha en delkomponent för att avgöra hur många av en artikel som är i ordern. Kvantiteten ska begränsas till ett tal så att de extraherade data omedelbart är tillgängliga för klientprogrammet efter namn.

Ett villkor används som en textmatchning, antingen med exakt matchning (till exempel en listentitet) eller genom reguljära uttryck (till exempel en reguljär uttrycksentitet eller en fördefinierad entitet).

Genom att använda ett villkor extraheras endast text som matchar det villkoret.

1. Välj **Entiteter** och välj sedan `Order` entiteten.
1. Välj **+ Lägg till** `Quantity` komponent och ange sedan namnet och välj `Order` sedan Retur för att lägga till den nya delkomponenten i entiteten.
1. Efter meddelandet om lyckade alternativ väljer du pennan Begränsning i **avancerade alternativ.**
1. Välj det fördefinierade numret i listrutan.

    ![Skapa kvantitetentitet med fördefinierat tal som villkor.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    Entiteten `Quantity` tillämpas när texten matchar det fördefinierade talettiteten.

    Entiteten med begränsningen skapas men tillämpas ännu inte på exempelyttrandena.

    > [!NOTE]
    > En delkomponent kan kapslas inom en delkomponent på upp till 5 nivåer. Även om detta inte visas i den här artikeln, är den tillgänglig från portalen och API: et.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Etikettexempel yttrande för att lära LUIS om entiteten

1. Välj **Avsikter** från den vänstra navigeringen och välj sedan **OrderPizza-avsikten.** De tre talen i följande yttranden är märkta `Order` men ligger visuellt under entitetsraden. Den lägre nivån innebär att enheterna hittas men `Order` inte betraktas som en del av företaget.

    ![Fördefinierat tal hittas men betraktas inte som en annan än orderentiteten ännu.](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Märk siffrorna med `Quantity` entiteten `2` genom att välja i `Quantity` exempelutseendet och sedan välja från listan. Märk `6` och `1` i samma exempel yttrande.

    ![Etiketttext med kvantitetsentitet.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Träna appen för att tillämpa entitetsändringarna i appen

Välj **Träna** om du vill träna appen med de här nya yttrandena. Efter träningen `Quantity` förutses delkomponenten korrekt i komponenten. `Order` Denna korrekta förutsägelse indikeras med en heldragen linje.

![Träna appen och granska sedan exempelyttrandena.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

Nu har ordern några detaljer som kan extraheras (storlek, kvantitet och total ordertext). Det finns ytterligare raffinering av `Order` enheten såsom pizza pålägg, typ av skorpa, och sidoorder. Var och en av dessa bör skapas `Order` som delkomponenter till entiteten.

## <a name="test-the-app-to-validate-the-changes"></a>Testa appen för att validera ändringarna

Testa appen med **Test** hjälp av den interaktiva testpanelen. Med den här processen kan du ange ett nytt uttryck och sedan visa förutsägelseresultaten för att se hur väl den aktiva och tränade appen fungerar. Avsikten förutsägelse bör vara ganska säker (över 70%) och enhetsutvinningen bör ta `Order` upp åtminstone enheten. Informationen om orderentiteten kan saknas eftersom 5 yttranden inte räcker för att hantera alla ärenden.

1. Välj **Test** i det övre navigeringsfältet.
1. Ange uttryck `deliver a medium veggie pizza` och välj Retur. Den aktiva modellen förutspådde rätt avsikt med över 70% förtroende.

    ![Ange ett nytt uttryck för att testa avsikten.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Välj **Granska** om du vill visa entitetsprognoser.

    ![Visa entitetsprognoser på den interaktiva testpanelen.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    Storleken identifierades korrekt. Kom ihåg att exempelyttrandena i avsikten `OrderPizza` `medium` inte har ett exempel på som `SizeDescriptor` en storlek, men använder en deskriptor av en fraslista som innehåller medium.

    Kvantiteten är inte korrekt förutsedd. Du kan åtgärda detta i klientprogrammet som standardstorlek till en (1) om ingen storlek returneras i LUIS-förutsägelsen.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicera appen för att komma åt den från HTTP-slutpunkten

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Hämta avsikts- och entitetsprognos från HTTP-slutpunkten

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Gå till slutet av WEBBADRESSEN i adressfältet och ersätt _YOUR_QUERY_HERE_ med samma fråga som du angav på den interaktiva testpanelen.

    `deliver a medium veggie pizza`

    Den senaste frågesträngparametern är `query`, uttrycksfrågan . **query**

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Relaterad information

* [Handledning - avsikter](luis-quickstart-intents-only.md)
* [Koncept - entitetskonceptuell](luis-concept-entity-types.md) information
* [Concept - funktioner](luis-concept-feature.md) begreppsmässig information
* [Så här tränar du](luis-how-to-train.md)
* [Så här publicerar du](luis-how-to-publish-app.md)
* [Så här testar du i LUIS-portalen](luis-interactive-test.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudien använder appen en dator som lärt sig entiteten för att hitta avsikten med en användares yttrande och extrahera information från det uttrycket. Med hjälp av den maskininlärda entiteten kan du dela upp information om entiteten.

> [!div class="nextstepaction"]
> [Lägg till en fördefinierad keyPhrase-entitet](luis-quickstart-intent-and-key-phrase.md)
