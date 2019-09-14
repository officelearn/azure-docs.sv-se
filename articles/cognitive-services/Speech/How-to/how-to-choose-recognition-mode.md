---
title: Så här väljer du Taligenkänning i Bing igenkännings läge | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Så här väljer du det bästa igenkännings läget i Taligenkänning i Bing.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965680"
---
# <a name="bing-speech-recognition-modes"></a>Taligenkänning i Bing igenkännings lägen

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing-tal till text-API: er stöder flera lägen för tal igenkänning. Välj det läge som ger bäst igenkännings resultat för ditt program.

| Läge | Beskrivning |
|---|---|
| *interaktiv* | "Kommando-och kontroll"-igenkänning för interaktiva användar program scenarier. Användare pratar om korta fraser avsedda som kommandon i ett program. |
| *Diktering* | Kontinuerlig igenkänning av dikterings scenarier. Användare pratar längre meningar som visas som text. Användarna använder en mer formell tal format. |
| *samtalet* | Kontinuerlig igenkänning för att skriva över samtal mellan människor. Användarna använder en mindre formell tal stil och kan växla mellan längre meningar och kortare fraser.

> [!NOTE]
> Dessa lägen gäller när du använder rest- [API: er](../GetStarted/GetStartedREST.md). [Klient biblioteken](../GetStarted/GetStartedClientLibraries.md) använder olika parametrar för att ange tolknings läge. Mer information finns i det klient bibliotek som du väljer.

Mer information finns på sidan [igenkännings lägen](../concepts.md#recognition-modes) .
