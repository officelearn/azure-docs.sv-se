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
ms.openlocfilehash: a72e5055a3b0f858a732ec28eeab511b09dd450c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400152"
---
# <a name="scenario-availability"></a>Tillgänglighet för scenario

Speech SDK har många scenarier i en mängd olika programmeringsspråk och miljöer. Alla scenarier är inte tillgängliga i alla programmeringsspråk eller alla miljöer ännu. Nedan listas tillgängligheten för varje scenario.

- **Taligenkänning (SR), fraslista, avsikt, översättning och lokala behållare**
  - C++/Windows & Linux & macOS
  - C# (Framework & .NET Core)/Windows & UWP & Unity & Xamarin & Linux & macOS
  - Java (Jre och Android)
  - JavaScript (Brower och NodeJS)
  - Python
  - Swift
  - Objective-C  
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
