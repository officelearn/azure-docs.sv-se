---
title: Hantera inställningarna för ditt konto i LUIS | Microsoft Docs
description: Använda LUIS webbplats för att hantera inställningarna för ditt konto.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/08/2018
ms.author: diberry
ms.openlocfilehash: f3086f09e29664b816ba709fc5cda75d7b11d1b4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035257"
---
# <a name="manage-account-and-authoring-key"></a>Hantera konton och redigering av nyckel
De två viktig information för ett konto med LUIS är användarkontot och nyckeln för redigering. Din inloggningsinformation hanteras på [account.microsoft.com](https://account.microsoft.com). Redigering nyckeln hanteras från den [LUIS](luis-reference-regions.md) webbplats **inställningar** sidan. 

## <a name="authoring-key"></a>Redigera nyckel

Den här enda, regionspecifika redigering nyckeln på den **inställningar** sidan kan du skapa alla dina appar från den [LUIS](luis-reference-regions.md) webbplats samt de [redigera API: er](https://aka.ms/luis-authoring-api). Förenkla processen redigering nyckeln kan göra en [begränsad](luis-boundaries.md) antalet endpoint frågar varje månad. 

![LUIS inställningssidan](./media/luis-how-to-account-settings/account-settings.png)

Redigering nyckeln används för alla appar som du äger, samt alla appar som du står som medarbetare.

## <a name="authoring-key-regions"></a>Redigera viktiga regioner
Redigering nyckeln är specifika för den [redigering region](luis-reference-regions.md#publishing-regions). Nyckeln fungerar inte i en annan region. 

## <a name="reset-authoring-key"></a>Återställ redigering nyckel
Om redigering nyckeln äventyras, kan du återställa nyckeln. Nyckeln har återställts på alla dina appar på den [LUIS](luis-reference-regions.md) webbplats. Om du skapar dina appar authoring-API: er, måste du ändra värdet för `Ocp-Apim-Subscription-Key` till den nya nyckeln. 

## <a name="delete-account"></a>Ta bort konto
Se [datalagring och borttagning av](luis-concept-data-storage.md#accounts) information om vilka data tas bort när du tar bort ditt konto. 

## <a name="next-steps"></a>Nästa steg

Läs mer om din [redigering nyckeln](luis-concept-keys.md#authoring-key). 

