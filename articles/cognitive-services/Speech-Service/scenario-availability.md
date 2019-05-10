---
title: Scenariot tillgänglighet – Speech Services
titlesuffix: Azure Cognitive Services
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: chrisbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cbasoglu
ms.openlocfilehash: d844b171ff99dc97e5d1107bcb745f9e8d5b3e9d
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519831"
---
# <a name="scenario-availability"></a>Scenario-tillgänglighet

Tal-tjänst-SDK-funktioner många scenarier i en mängd olika programmeringsspråk och miljöer.  Inte alla scenarier är tillgängliga i alla programmeringsspråk eller alla miljöer ännu.  Nedan visas tillgängligheten för varje scenario.

- **Taligenkänning (SR), frasen lista, avsikt, översättning och en lokal behållare**
  - Alla språk/programmeringsmiljöer där det finns en pilknappen <img src="media/index/link.jpg" height="15" width="15"></img> i tabellen Snabbstart [här](https://aka.ms/csspeech).
- **Tal**
  - C++/Windows & Linux
  - C#/Windows
  - Text till tal-REST-API kan användas i alla andra situationer.
- **Aktivera Word (nyckelordet Spotter/KWS)**
  - C++/Windows & Linux
  - C#/ Windows och Linux
  - Python/Windows och Linux
  - Java/Windows- och Linux- och Android (tal enheter SDK)
  - Wake Word (nyckelordet Spotter/KWS) funktioner fungerar med en Mikrofontyp, officiella KWS stöder finns för närvarande begränsad till mikrofonen-matriser i Azure Kinect DK-maskinvara eller tal Devices SDK
- **Röst första virtuella assistenter**
  - C++/ Windows och Linux och macOS
  - C#/Windows
  - Java/Windows och Linux och macOS och Android (tal enheter SDK)
- **Konversationen avskrift**
  - C++/Windows & Linux
  - C#(Framework och .NET Core) / Windows- och UWP- och Linux
  - Java/Windows- och Linux- och Android (tal enheter SDK)
- **Callcenter avskrift**
  - REST API och kan användas i alla situationer
- **Codec komprimerade ljudindata**
  - C++/Linux
  - C#/Linux
  - Java-/ Linux- och Android
