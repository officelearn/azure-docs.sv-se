---
title: Funktioner – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) för att lägga till app-funktioner som kan förbättra identifieringen eller förutsägelsen av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592312"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Använd funktioner för att förstärka signalen av ord listan

Du kan lägga till funktioner i LUIS-appen för att förbättra noggrannheten. Funktioner hjälper LUIS genom att tillhandahålla tips om att vissa ord och fraser är en del av en app Domain-vokabulär.

Granska [begreppen](luis-concept-feature.md) för att förstå när och varför du ska använda en funktion.

## <a name="add-phrase-list-as-a-feature"></a>Lägg till fras lista som en funktion

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj **bygge**och välj sedan **funktioner** i appens vänstra panel.

1. På sidan **funktioner** väljer du **+ skapa**.

1. I dialog rutan **Skapa ny fras List funktion** anger du ett namn som t `Cities` . ex.. I rutan **värde** anger du exempel på städer, till exempel `Seattle` . Du kan ange ett värde i taget eller en uppsättning värden avgränsade med kommatecken och sedan trycka på **RETUR**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till funktioner (fras lista) i städer](./media/luis-add-features/add-phrase-list-cities.png)

    När du har angett tillräckligt många värden för LUIS visas förslag. Du kan **lägga till alla** föreslagna värden eller välja enskilda villkor.

1. Behåll **dessa värden är utbytbara** om fraserna kan användas utbytbart.

1. Fras listan kan tillämpas på hela appen med den **globala** inställningen eller till en speciell modell (avsikt eller entitet). Om du skapar fras listan, som en _funktion_ från ett avsikt eller en entitet, är växlingen inte inställd för global. I det här fallet är syftet med växlingen att funktionen bara är lokal för den modellen, vilket innebär att den _inte är global_ för programmet.

1. Välj **Done** (Klar). Den nya funktionen läggs till på sidan med **ml-funktioner** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en fras lista från kontext verktygsfältet på sidan med **ml-funktioner** .

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en funktion kan du [träna och testa appen](luis-interactive-test.md) igen för att se om prestandan förbättras.
