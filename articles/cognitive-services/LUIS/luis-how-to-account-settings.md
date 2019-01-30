---
title: Hantera inställningar
titleSuffix: Language Understanding - Azure Cognitive Services
description: Använd LUIS webbplats för att hantera dina inställningar och redigering nyckeln som används för alla dina appar.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 51b0d3f753ab89e5809e610f5754355d1c5090b0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228635"
---
# <a name="manage-account-and-authoring-key"></a>Hantera konton och redigering av nyckel
De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Din inloggningsinformation hanteras på [account.microsoft.com](https://account.microsoft.com). Redigering nyckeln hanteras från den [LUIS](luis-reference-regions.md) webbplats **inställningar** sidan. 

## <a name="authoring-key"></a>Redigera nyckel

Den här enda, regionspecifika redigering nyckeln på den **inställningar** sidan kan du skapa alla dina appar från den [LUIS](luis-reference-regions.md) webbplats samt de [redigera API: er](https://aka.ms/luis-authoring-api). Förenkla processen redigering nyckeln kan göra en [begränsad](luis-boundaries.md) antalet endpoint frågar varje månad. 

[![LUIS inställningssidan](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Redigering nyckeln används för alla appar som du äger, samt alla appar som du står som medarbetare.

## <a name="authoring-key-regions"></a>Redigera viktiga regioner
Redigering nyckeln är specifika för den [redigering region](luis-reference-regions.md#publishing-regions). Nyckeln fungerar inte i en annan region. 

## <a name="reset-authoring-key"></a>Återställ redigering nyckel
Om redigering nyckeln äventyras, kan du återställa nyckeln. Nyckeln har återställts på alla dina appar på den [LUIS](luis-reference-regions.md) webbplats. Om du skapar dina appar authoring-API: er, måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln. 

## <a name="delete-account"></a>Ta bort konto
Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto. 

## <a name="next-steps"></a>Nästa steg

Läs mer om din [redigering nyckeln](luis-concept-keys.md#authoring-key). 

