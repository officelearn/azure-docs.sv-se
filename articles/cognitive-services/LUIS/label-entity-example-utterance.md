---
title: Etikett för entitets exempel uttryck
description: Lär dig hur du förser en enhet för maskin inlärning med del komponenter i ett exempel på en uttryck på LUIS-portalen.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 0181057bd693280223806e9b5b7cd8c7f7345f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683776"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Etiketten maskin inlärnings enhet i ett exempel uttryck

Att märka en entitet i ett exempel uttryck ger LUIS ett exempel på vad entiteten är och var entiteten kan visas i uttryck.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Etikett exempel på yttranden från sidan för avsikts information

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj den avsikt som har det yttranden som du vill märka för extrahering med en entitet.
1. Välj den text som du vill lägga till en etikett och välj sedan entiteten.

## <a name="label-with-the-entity-palette-visible"></a>Etikett med entitetens palett synlig

När du har planerat schemat med entiteter ska **entitetens palett** vara synlig medan du märker dem. **Entitetens palett** är en påminnelse om vilka entiteter du har planerat att extrahera.

Om du vill komma åt **entiteten entitet**väljer du **@** symbolen i kontext verktygsfältet ovanför exempel uttryck-listan.

> [!div class="mx-imgBorder"]
> ![Skärm bild av paletten för entity på sidan information om avsikt.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="label-entity-from-in-place-menu"></a>Etiketten entitet från menyn på plats

Överväg exemplet uttryck, `hi, please I want a cheese pizza in 20 minutes` .

1. Välj den vänstra texten och välj den högra texten i entiteten. Välj sedan den entitet som du vill etikettera med på menyn på plats.

    > [!div class="mx-imgBorder"]
    > ![Etiketten slutförd maskin inlärnings enhet](media/label-utterances/label-steps-in-place-menu.png)


## <a name="label-entity-from-entity-palette"></a>Etiketten entitet från Entity-paletten

Entity-paletten erbjuder ett alternativ till föregående etikett upplevelse. Det gör att du kan fylla i text för att omedelbart förse den med en entitet.

1. Öppna paletten entitet genom att välja på **@** symbolen längst upp till höger i uttryck-tabellen.

2. Välj entiteten från paletten som du vill namnge. Den här åtgärden visas visuellt med en ny markör. Markören följer musen när du flyttar i LUIS-portalen.

3. I uttryck-exemplet _målar_ du entiteten med markören.

    > [!div class="mx-imgBorder"]
    > ![Entity-palett för entiteten för maskin inlärning](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Lägga till entitet som en funktion från entitetens palett

I det nedre avsnittet i Entity kan du lägga till funktioner i den markerade entiteten. Du kan välja från alla befintliga entiteter och fras listor eller skapa en ny fras lista.

> [!div class="mx-imgBorder"]
> ![Skärm bild av entitetens palett med entitet som en funktion](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Etikettering av enhets roller

Enhets roller märks med hjälp av **Entity-paletten**.

1. På sidan information om avsikt väljer du **paletten entitet** i verktygsfältet kontext.
1. När paletten entitet öppnas väljer du entiteten från listan med entiteter.
1. Under listan över entiteter väljer du en befintlig roll.
1. I exemplet uttryck text etiketterar du texten med entitets rollen.

## <a name="review-labeled-text"></a>Granska etiketterad text

Efter märkningen granskar du exemplet uttryck och ser till att det valda text intervallet har marker ATS med den valda entiteten. Den heldragna linjen anger att texten har märkts.

> [!div class="mx-imgBorder"]
> ![Etiketten slutförd enhet för maskin inlärning](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Bekräfta förväntad entitet

Om det finns en prickad ruta runt textens omfång, anger den att texten är förväntad men _inte märkt än_. Om du vill omvandla förutsägelsen till en etikett väljer du raden uttryck och väljer sedan **Bekräfta entiteter** i kontext verktygsfältet.

## <a name="relabeling-over-existing-entities"></a>Etikettera över befintliga entiteter

Om du ometiketterar text som redan är märkt kan LUIS dela eller slå samman befintliga etiketter.

## <a name="labeling-for-punctuation"></a>Etiketter för interpunktion

Du behöver inte ange en etikett för interpunktion. Använd [program inställningar](luis-reference-application-settings.md) _ord former_ för att kontrol lera om interpunktion påverkar uttryck förutsägelser.

## <a name="unlabel-entities"></a>Etikettera entiteter

> [!NOTE]
> Endast enheter som har lärts till enheten kan vara omärkta.

Om du vill omärkta en entitet väljer du entiteten och väljer sedan **omärkt** på menyn på plats.

> [!div class="mx-imgBorder"]
> ![Skärm bild som visar omärkt entitet](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Automatisk etikettering för överordnade och underordnade entiteter

Om du etiketterar en överordnad entitet, kommer alla underentiteter som kan förutsägas baserat på den aktuella utbildade versionen att märkas.

Om du anger en etikett för en underordnad etikett så märks den överordnade automatiskt.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Automatisk etikettering för entiteter som inte är från en dator

Enheter som inte är baserade på den här enheten är fördefinierade entiteter, reguljära uttryck entiteter, lista entiteter och mönster. alla entiteter. Dessa etiketteras automatiskt av LUIS så att de inte behöver anges manuellt av användarna.

## <a name="entity-prediction-errors"></a>Enhets förutsägelse fel

Fel vid enhets förutsägelse indikerar att den förväntade enheten inte matchar den märkta entiteten. Detta visualiseras med en varnings indikator bredvid uttryck.

> [!div class="mx-imgBorder"]
> ![Entity-palett för entiteten för maskin inlärning](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Nästa steg

Använd [instrument panelen](luis-how-to-use-dashboard.md) och [Granska slut punkts yttranden](luis-how-to-review-endpoint-utterances.md) för att förbättra förutsägelse kvaliteten för din app.