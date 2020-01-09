---
title: Etikett för entitets exempel uttryck
titleSuffix: Azure Cognitive Services
description: Lär dig hur du etiketterar en enhets medveten entitet med del komponenter i ett exempel på en uttryck på LUIS-portalen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: f7d6e98205afad2ed2c4aea30e635254f79acaa1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448093"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Märk enhets enheten i ett exempel uttryck

Att märka en entitet i ett exempel uttryck ger LUIS ett exempel på vad entiteten är och var entiteten kan visas i uttryck.

## <a name="labeling-machine-learned-entity"></a>Etikettering av datorn – lärt sig entiteten

Överväg frasen `hi, please I want a cheese pizza in 20 minutes`.

1. Välj den vänstra texten och välj den högra texten i entiteten. Välj sedan den entitet som du vill etikettera med, i det här fallet Slutför ordningen. Den _fullständiga ordningen_ är märkt i följande bild.

    > [!div class="mx-imgBorder"]
    > ![etiketten har slutförts efter att enheten](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Välj entiteten i popup-fönstret. Etiketten fullständig pizza order-entitet innehåller alla ord (från vänster till höger på engelska) som är märkta.

## <a name="review-labeled-text"></a>Granska etiketterad text

Efter märkningen granskar du exemplet uttryck och ser till att det valda text intervallet har marker ATS med den valda entiteten. Den heldragna linjen anger att texten har märkts.

> [!div class="mx-imgBorder"]
> ![märkta den fullständiga enheten](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Bekräfta förväntad entitet

Om det finns en prickad ruta runt text området och entitetsnamnet är över uttryck, anger det att texten är förutsägd men _inte märkt än_. Om du vill omvandla förutsägelsen till en etikett väljer du raden uttryck och väljer **Bekräfta enhets förutsägelser**.

> [!div class="mx-imgBorder"]
> ![förutse att enheten har lärts](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Alternativt kan du välja entitetsnamnet ovanför texten och sedan välja **Bekräfta förutsägelse** på menyn som visas.

> [!div class="mx-imgBorder"]
> ![förutse att datorn har lärts med meny](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Etiketten entitet genom att måla med pekarens färgpalett

Entity-paletten erbjuder ett alternativ till föregående etikett upplevelse. Det gör att du kan fylla i text för att omedelbart förse den med en entitet.

1. Öppna paletten entitet genom att välja på ikonen överstryknings ikonen längst upp till höger i uttryck-tabellen.

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Välj entitets komponenten. Den här åtgärden visas visuellt med en ny markör. Markören följer musen när du flyttar i portalen.

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. I uttryck-exemplet _målar_ du entiteten med markören.

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Namnge del komponenter på en dator som har lärts in

Del komponenter i entiteter är märkta exakt på samma sätt som entiteter på översta nivån. När du väljer text är de entiteter som är tillgängliga i popup-fönstret i förhållande till kontexten där texten visas. Om du till exempel har en enhet på 5-nivå som är lärts och du väljer text som har märkts med den första och andra nivån (som anges av ett namngett entitetsnamn under exemplet uttryck), är entiteterna som är tillgängliga i popup-fönstret begränsade till kontext för komponenter på den tredje nivån. Om du vill märka texten med andra entiteter väljer du **etikett som ett alternativ för entiteten** .

> [!div class="mx-imgBorder"]
> ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Del komponenter kan bara märkas om överordnad också är märkt.

## <a name="labeling-entity-roles"></a>Etikettering av enhets roller

Enhets roller märks med hjälp av Entity-paletten.

1. På sidan information om avsikt väljer du **paletten entitet** i verktygsfältet kontext.
1. När paletten entitet öppnas väljer du entiteten från listan med entiteter.
1. Flytta till **entitetens kontrollant**och välj antingen en befintlig roll eller skapa en ny roll.
1. I exemplet uttryck text etiketterar du texten med entitets rollen. 

## <a name="unlabel-entities"></a>Etikettera entiteter

Om du vill omärkta en entitet väljer du enhetens namn under texten och väljer sedan **omärkt etikett**. Om entiteten som du försöker att etikettera har namngett del komponenter måste del komponenterna vara omärkta först.

## <a name="editing-labels-using-the-entity-palette"></a>Redigera etiketter med Entity-paletten

Om du gör ett misstag medan du märker det är entitetens palett ett enkelt verktyg som gör det möjligt att redigera snabbt. Om till exempel en enhets etikett sträcker sig över ett extra ord av misstag, och det redan har märkt del komponenter, kan du använda entitetens palett för att använda det kortare intervallet av ord.

Ett exempel:

1. Pizza Type-komponenten sträcker sig över "ost-pizza med", som innehåller ett extra felaktigt ord – "med"

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/edit-label-with-palette-1.png)

2. Använd Entity-paletten för att välja pizza-typ och pensel över "ost-pizza". Resultatet är att endast ost-pizza är märkt som pizza-typ nu.

    > [!div class="mx-imgBorder"]
    > ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Etiketter för matchande text-entiteter

Matchande textentiteter är fördefinierade entiteter, reguljära uttryck entiteter, lista entiteter och mönster. alla entiteter. Dessa etiketteras automatiskt av LUIS så att de inte behöver anges manuellt av användarna.

## <a name="entity-prediction-errors"></a>Enhets förutsägelse fel

Fel vid enhets förutsägelse indikerar att den förväntade enheten inte matchar den märkta entiteten. Detta visualiseras med en varnings indikator bredvid uttryck.

> [!div class="mx-imgBorder"]
> ![-färgpalett för enhet som har registrerats av enheten](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Nästa steg

Använd [instrument panelen](luis-how-to-use-dashboard.md) och [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för att förbättra förutsägelse kvaliteten för din app.