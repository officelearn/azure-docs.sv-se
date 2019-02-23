---
title: Så här väljer du Bing Speech erkännande läge | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Så här att välja det bästa läget för taligenkänning i Bing-taligenkänning.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669728"
---
# <a name="bing-speech-recognition-modes"></a>Lägen för taligenkänning för Bing-tal

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing-tal till Text-API: er har stöd för flera lägen för taligenkänning. Välj det läge som ger det bästa av resultatet för ditt program.

| Läge | Beskrivning |
|---|---|
| *interaktiv* | ”Kommando och kontroll” erkännande för interaktiv användare Programscenarier. Användare talar korta fraser som är avsett för kommandon till ett program. |
| *diktering* | Kontinuerlig igenkänning av diktering scenarier. Användare talar längre meningar som visas som text. Användare anta ett mer formella samtalsstil. |
| *konversationen* | Kontinuerlig erkännande för transkribera konversationer mellan människor. Användare använda en mindre formell samtalsstil och kan växla mellan längre meningar och kortare fraser.

> [!NOTE]
> Dessa lägen kan användas när du använder den [REST API: er](../GetStarted/GetStartedREST.md). Den [klientbibliotek](../GetStarted/GetStartedClientLibraries.md) använda olika parametrar för att ange erkännande läge. Mer information finns i klientbiblioteket för ditt val.

Mer information finns i den [erkännande lägen](../concepts.md#recognition-modes) sidan.
