---
title: Scenario tillgänglighet – tal tjänst
titleSuffix: Azure Cognitive Services
description: Referens för regioner i tal-tjänsten.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6ec31df7cef8391728eae7845f64f55bb1c6466a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491349"
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
  - Swift
  - Objective-C
  - TTS-REST API kan användas i alla andra situationer.
- **Nyckelord upptäcka (KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Speech-enheter SDK)
  - Nyckelordet upptäcka (KWS) fungerar med valfri mikrofon typ, officiell KWS support, men är för närvarande begränsad till de mikrofoner som finns i Azure Kinect DK-maskinvara eller tal enheter SDK
- **Röst assistenter**
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
