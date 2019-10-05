---
title: Scenario tillgänglighet – tal tjänst
titleSuffix: Azure Cognitive Services
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: af5bb7126af65a755cb1d58788d39cb8bdcbbb3b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959025"
---
# <a name="scenario-availability"></a>Scenario tillgänglighet

Tjänsten Speech service SDK innehåller många olika scenarier i olika programmeringsspråk och miljöer.  Alla scenarier är inte tillgängliga för närvarande i alla programmeringsspråk eller i alla miljöer än.  Nedan visas tillgänglighet för varje scenario.

- **Tal igenkänning (SR), fras lista, avsikt, översättning och lokala behållare**
  - Alla programmeringsspråk/miljöer där det finns en pil-länk <img src="media/index/link.jpg" height="15" width="15"></img> i snabb starts tabellen [här](https://aka.ms/csspeech).
- **Text till tal (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (JRE och Android)
  - Python
  - SWIFT
  - Objective-C
  - TTS-REST API kan användas i alla andra situationer.
- **Aktiverings ord (nyckelord spotter/KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech-enheter SDK)
  - Funktionen Wake Word (nyckelord spotter/KWS) fungerar med valfri mikrofon typ, officiell KWS-support, är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller tal enheter SDK
- **Röst – första virtuella assistenten**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (tal enheter SDK)
- **Konversations avskrift**
  - C++/Windows & Linux
  - C#(Framework & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Speech-enheter SDK)
- **Svars Center-avskrift**
  - REST API och kan användas i alla situationer
- **Codec-komprimerad ljud inspelning**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android och iOS
