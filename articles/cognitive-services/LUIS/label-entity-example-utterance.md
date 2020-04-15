---
title: Uttryck för uttryck för etikettentitet
description: Lär dig hur du märker en datorinlärd entitet med underkomponenter i ett exempelyttrande på en avsiktsdetaljsida i LUIS-portalen.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: ea5fbea902c9694d9a8a6a8a5bffcf5e7234bbbd
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382405"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etikett maskininlärd entitet i ett exempel yttrande

Om du etiketterar en entitet i ett exempelyttrande får LUIS ett exempel på vad entiteten är och var entiteten kan visas i uttrycket.

## <a name="labeling-machine-learned-entity"></a>Märkning av maskininlärd entitet

Tänk på `hi, please I want a cheese pizza in 20 minutes`frasen.

1. Markera den vänstra texten och markera sedan texten till höger och högst upp i entiteten och välj sedan den entitet som du vill märka med i det här fallet Slutförd ordning. Den _fullständiga ordningen_ är märkt i följande bild.

    > [!div class="mx-imgBorder"]
    > ![Etikett komplett maskininlärd enhet](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Välj entiteten i popup-fönstret. Den märkta fullständiga pizzaordereniteten innehåller alla ord (från vänster till höger på engelska) som är märkta.

## <a name="review-labeled-text"></a>Granska märkt text

När du har etiketterat granskar du exempelutsiktet och ser till att det valda textintervallet har understrukits med den valda entiteten. Den heldragna linjen anger att texten har märkts.

> [!div class="mx-imgBorder"]
> ![Märkt komplett maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Bekräftad förväntad entitet

Om det finns en prickad ruta runt textintervallet och entitetsnamnet ligger ovanför uttrycket, betyder det att texten förutspås men _inte är märkt ännu_. Om du vill omvandla förutsägelsen till en etikett markerar du uttrycksraden och väljer sedan **Bekräfta entitetsförutsägelser**.

> [!div class="mx-imgBorder"]
> ![Förutsäg fullständig maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

Du kan också markera entitetsnamnet ovanför texten och sedan välja **Bekräfta förutsägelse** på menyn som visas.

> [!div class="mx-imgBorder"]
> ![Förutsäg fullständig maskininlärd entitet med meny](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Etikettentitet genom att måla med entitetspalettmarkör

Entitetspaletten erbjuder ett alternativ till den tidigare märkningsupplevelsen. Det låter dig pensel över text för att omedelbart märka den med en entitet.

1. Öppna entitetspaletten genom att välja ikonen Överst till höger i uttryckstabellen.

    > [!div class="mx-imgBorder"]
    > ![Entitetspalett för maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Välj entitetskomponenten. Den här åtgärden visas visuellt med en ny markör. Markören följer musen när du flyttar i portalen.

    > [!div class="mx-imgBorder"]
    > ![Entitetspalett för maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. I exempelutseendet _målar du_ entiteten med markören.

    > [!div class="mx-imgBorder"]
    > ![Entitetspalett för maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Märka underkomponenter till en maskin som lärt sig entitet

Underkomponenter i entiteter är märkta på exakt samma sätt som entiteter på den högsta nivån. När du markerar text är de entiteter som är tillgängliga i popup-fönstret relativa till den kontext där texten visas. Om du till exempel har en datorinlärd entitet på 5-nivå och du markerar text som har märkts med 1:a och 2:a nivån (indikerad av ett märkt entitetsnamn under exempelyttrandet), är de entiteter som är tillgängliga i popup-fönstret begränsade till kontexten för komponenter på tredje nivån. Om du vill märka texten med andra entiteter väljer du **Etikett som ett annat entitetsalternativ.**

> [!div class="mx-imgBorder"]
> ![Entitetspalett för maskininlärd entitet](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

Underkomponenter kan bara märkas om den överordnade också är märkt.

## <a name="labeling-entity-roles"></a>Märka entitetsroller

Entitetsroller är märkta med hjälp av entitetspaletten.

1. På sidan Avsiktsdetaljer väljer du **entitetspaletten** i kontextverktygsfältet.
1. När entitetspaletten har öppnats väljer du entiteten i listan över entiteter.
1. Flytta till **entitetsinspektören**och välj antingen en befintlig roll eller skapa en ny roll.
1. I exempelutsyttringstexten märker du texten med entitetsrollen.

## <a name="labeling-for-punctuation"></a>Märkning för interpunktion

Du behöver inte märka för interpunktion. Använd [programinställningar för](luis-reference-application-settings.md) att styra om interpunktion påverkar uttrycksförutsägelser.

## <a name="unlabel-entities"></a>Omärkta enheter

Om du vill ta upp en entitet markerar du entitetsnamnet under texten och väljer **Unlabel**. Om entiteten som du försöker avla har märkt underkomponenter måste underkomponenterna först vara omärkta.

## <a name="editing-labels-using-the-entity-palette"></a>Redigera etiketter med hjälp av entitetspaletten

Om du gör ett misstag när du etiketterar är entitetspaletten ett enkelt verktyg som möjliggör snabba redigeringar. Om till exempel en entitetsetikett sträcker sig över ett extra ord av misstag och den redan har märkt underkomponenter, kan du använda entitetspaletten för att pensla över den önskade kortare ordintervallet.

Ett exempel:

1. Pizza Typ subkomponent spänner "ost pizza med" som innehåller ett extra felaktigt ord - "med"

    > [!div class="mx-imgBorder"]
    > ![Entitetspalett för maskininlärd entitet](media/label-utterances/edit-label-with-palette-1.png)

2. Använd entitetspaletten för att plocka Pizza Type och borsta över "ostpizza". Resultatet är att endast ost pizza är märkt som Pizza Type nu.

    > [!div class="mx-imgBorder"]
    > ![Entitetspalett för maskininlärd entitet](media/label-utterances/edit-label-with-palette-2.png)

## <a name="labels-for-matching-text-entities"></a>Etiketter för matchande textentiteter

Matchningstextentiteter inkluderar fördefinierade entiteter, reguljära uttrycksentiteter, listentiteter och pattern.any-entiteter. Dessa är automatiskt märkta av LUIS så att de inte behöver märkas manuellt av användare.

## <a name="entity-prediction-errors"></a>Fel i entitetsprognos

Entitetsprognosfel indikerar att den förväntade entiteten inte matchar den märkta entiteten. Detta visualiseras med en varningsindikator bredvid uttrycket.

> [!div class="mx-imgBorder"]
> ![Entitetspalett för maskininlärd entitet](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Nästa steg

Använd [instrumentpanelen](luis-how-to-use-dashboard.md) och [granska slutpunktsyttranden](luis-how-to-review-endpoint-utterances.md) för att förbättra appens förutsägelsekvalitet.