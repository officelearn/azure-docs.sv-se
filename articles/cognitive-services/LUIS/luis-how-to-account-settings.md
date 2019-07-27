---
title: Hantera konto och nycklar – LUIS
titleSuffix: Azure Cognitive Services
description: De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Din inloggnings information hanteras på account.microsoft.com. Din redigerings nyckel hanteras från sidan LUIS Portal inställningar.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9164c94886488ac7dcadeb7894453daad5d8b322
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560493"
---
# <a name="manage-account-and-authoring-key"></a>Hantera konton och redigering av nyckel

De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Din inloggningsinformation hanteras på [account.microsoft.com](https://account.microsoft.com). Din redigerings nyckel hanteras från sidan [Luis](luis-reference-regions.md) Portal **Inställningar** .

## <a name="authoring-key"></a>Redigera nyckel

Med den här enskilda, landsspecifika redigerings nyckeln på sidan **Inställningar** kan du redigera alla dina appar från [Luis](luis-reference-regions.md) -portalen samt redigerings- [API: er](https://go.microsoft.com/fwlink/?linkid=2092087). Förenkla processen redigering nyckeln kan göra en [begränsad](luis-boundaries.md) antalet endpoint frågar varje månad.

[![LUIS inställningssidan](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Redigering nyckeln används för alla appar som du äger, samt alla appar som du står som medarbetare.

## <a name="authoring-key-regions"></a>Redigera viktiga regioner

Redigering nyckeln är specifika för den [redigering region](luis-reference-regions.md#publishing-regions). Nyckeln fungerar inte i en annan region.

## <a name="reset-authoring-key"></a>Återställ redigering nyckel

Om redigering nyckeln äventyras, kan du återställa nyckeln. Nyckeln återställs på alla dina appar på [Luis](luis-reference-regions.md) -portalen. Om du skapar dina appar authoring-API: er, måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln.

## <a name="delete-account"></a>Ta bort konto

Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto.

## <a name="next-steps"></a>Nästa steg

Läs mer om din [redigering nyckeln](luis-concept-keys.md#authoring-key).

