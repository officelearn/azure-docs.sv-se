---
title: Lägg till funktioner i LUIS program | Microsoft Docs
description: Använd Språkförståelse (LUIS) att lägga till appfunktioner som kan förbättra identifiering eller förutsägelser av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222961"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Använda funktioner för att förbättra prestanda för dina LUIS-app  

Du kan lägga till funktioner till din LUIS-app för att förbättra dess noggrannhet. Funktionerna bidrar LUIS genom att tillhandahålla tips att vissa ord och fraser är en del av en kategori. Om LUIS lär sig hur du identifierar en medlem i kategorin, kan det hantera de andra på samma sätt.

## <a name="add-phrase-list"></a>Lägg till frasen lista

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **skapa**, klicka sedan på **fras listor** i vänster panel i din app. 

    ![Fras lista navigering](./media/luis-add-features/phrase-list-nav.png)

2. På den **fras listor** klickar du på **Skapa ny frasen lista**. 
 
    ![Skapa ny frasen lista](./media/luis-add-features/create-new-phrase-list.png)
    
3. I den **lägga till frasen lista** dialogrutan skriver du ”städer” som namn på listan med fraser. I den **värdet** skriver värdena i listan med fraser. Du kan ange ett värde i taget eller en uppsättning värden, avgränsade med kommatecken och tryck sedan på **RETUR**.

    ![Lägg till frasen lista städer](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS kan föreslå relaterade värden för att lägga till i listan fras. Klicka på **rekommenderar** att hämta en grupp med föreslagna värden från relaterade till added value(s). Du kan klicka på någon av de föreslagna värdena eller klicka på **Lägg till alla** att lägga till dem alla.

    ![Fras listan över föreslagna värden](./media/luis-add-features/related-values.png)

5. Klicka på **dessa värden är utbytbara** om listvärden har lagts till frasen finns alternativ som är utbytbara.

    ![Fras listan över föreslagna värden](./media/luis-add-features/interchangeable.png)

6. Klicka på **Spara**. Listan med frasen ”städer” har lagts till i den **fras listor** sidan.

    ![Fras listan har lagts till](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Redigera frasen lista

Klicka på namnet på listan frasen på den **fras listor** sidan. I den **redigera frasen lista** dialogrutan som öppnas, göra någon krävs för redigering av ändringar och klicka sedan på **spara**.

 ![Fras listan har lagts till](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Ta bort frasen lista 

Klicka på ellipsen (***...*** ) i slutet av raden och välj **ta bort**.

 ![Ta bort lista som har lagts till](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Inaktivera frasen lista 

Klicka på ellipsen (***...*** ) i slutet av raden och välj **inaktivera**.

 ![Inaktivera lista som har lagts till](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Funktionen för mönster (återkommande uttryck) 
**Den här funktionen är inaktuell**. Nya funktioner i mönstret kan inte läggas till LUIS. Alla befintliga mönstret-funktioner som stöds till maj 2018. Bidra till standard LUIS-reguljärt uttryck matchar med en pull-begäran till den [identifierare fulltext Github-lagringsplatsen](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Nästa steg

Efter att lägga till, redigera, ta bort eller inaktivera en fras lista [träna och testa appen](luis-interactive-test.md) igen för att se om prestanda förbättras.
