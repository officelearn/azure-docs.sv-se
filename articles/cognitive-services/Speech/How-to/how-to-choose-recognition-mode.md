---
title: Så här väljer du Recognition läge | Microsoft Docs
description: Så här väljer du det bästa recognition-läget.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352173"
---
# <a name="speech-recognition-modes"></a>Tal recognition lägen

Microsofts *tal till Text* API: er stöder flera lägen för taligenkänning. Välj det läge som ger de bästa resultat för ditt program.

| Läge | Beskrivning |
|---|---|
| *interaktiva* | ”Kommando- och” Igenkännande för Programscenarier för interaktiv användare. Användare tala fraser som är avsedda som kommandon till ett program. |
| *Dictation* | Kontinuerlig Igenkännande dictation scenarier. Användare tala längre meningar som visas som text. Användare anta en mer formella tala. |
| *konversationen* | Kontinuerlig Igenkännande för skriva konversationer mellan människor. Användare anta en mindre formella tala och kan växla mellan längre meningar och kortare fraser.

> [!NOTE]
> Dessa lägen kan användas när du använder den [REST API: er](../GetStarted/GetStartedREST.md). Den [klientbibliotek](../GetStarted/GetStartedClientLibraries.md) använda olika parametrar för att ange recognition läge. Mer information finns i klientbiblioteket önskat.

Mer information finns i [Recognition lägen](../concepts.md#recognition-modes) sidan.
