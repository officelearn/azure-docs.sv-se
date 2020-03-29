---
title: Tillgänglighet för scenario - Taltjänst
titleSuffix: Azure Cognitive Services
description: Speech SDK har många scenarier i en mängd olika programmeringsspråk och miljöer. Alla scenarier är inte tillgängliga i alla programmeringsspråk eller alla miljöer ännu. Nedan listas tillgängligheten för varje scenario.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: cbasoglu
ms.openlocfilehash: 6f8f892e7ca81881b0cc00e1708e3f05052c573c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76264211"
---
# <a name="scenario-availability"></a>Tillgänglighet för scenario

Speech SDK har många scenarier i en mängd olika programmeringsspråk och miljöer. Alla scenarier är inte tillgängliga i alla programmeringsspråk eller alla miljöer ännu. Nedan listas tillgängligheten för varje scenario.

- **Taligenkänning (SR), fraslista, avsikt, översättning och lokala behållare**
  - Alla programmeringsspråk/miljöer där det finns en pillänk <img src="media/index/link.jpg" height="15" width="15"></img> i snabbstartstabellen [här](https://aka.ms/csspeech).
- **Text-till-tal (TTS)**
  - C++/Windows & Linux
  - C#/Windows & UWP & Unity
  - Java (Jre och Android)
  - Python
  - Swift
  - Objective-C
  - TTS REST API kan användas i alla andra situationer.
- **Sökord spotting (KWS)**
  - C++/Windows & Linux
  - C#/Windows & Linux
  - Python/Windows & Linux
  - Java/Windows & Linux & Android (Talenheter SDK)
  - Funktionen för sökordsspottning (KWS) kan fungera med alla mikrofontyper, officiella KWS-stöd är dock för närvarande begränsat till de mikrofonmatriser som finns i Azure Kinect DK-maskinvaran eller Speech Devices SDK
- **Röstassistenter**
  - C++/Windows & Linux & macOS
  - C#/Windows
  - Java/Windows & Linux & macOS & Android (Speech Devices SDK)
- **Konversation Transkription**
  - C++/Windows & Linux
  - C# (Ram & .NET Core)/Windows & UWP & Linux
  - Java/Windows & Linux & Android (Talenheter SDK)
- **Konversation med flera enheter**
  - C++/Windows
  - C# (Ramverket & .NET Core)/Windows
- **Transkription av callcenter**
  - REST API och kan användas i alla situationer
- **Codec komprimerad ljudingång**
  - C++/Linux
  - C#/Linux
  - Java/Linux, Android och iOS
