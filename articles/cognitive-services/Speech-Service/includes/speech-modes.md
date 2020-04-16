---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422104"
---
## <a name="speech-modes"></a>Tallägen

**Interaktiva**
- Avsedd för kommando- och kontrollscenarier.
- Har ett segmenteringstidsvärde på X.
- I slutet av ett erkänt uttryck slutar tjänsten att bearbeta ljud från det begärande-ID:t och avslutar svängen. Anslutningen är inte stängd.
- Maxgränsen för igenkänning är 20s.
- Typiska Carbon samtal `RecognizeOnceAsync`att åberopa är .

**Konversation**
- Avsedd för längre löpigerkännanden.
- Har ett segmenteringstidsvärde på Y. (Y != X)
- Kommer att bearbeta flera fullständiga yttranden utan att avsluta svängen.
- Kommer att avsluta svängen för mycket tystnad.
- Carbon kommer att fortsätta med en ny begäran ID och spela upp ljud efter behov.
- Tjänsten kopplas med våld bort efter 10 minuters taligenkänning.
- Kol återansluts och spelas upp okräftat ljud igen.
- Åberopas i `StartContinuousRecognition`kol med .

**Diktamen**
- Tillåter användare att ange interpunktion genom att tala den.
- Anropas i Kol `EnableDictation` genom `SpeechConfig` att ange på objektet oavsett API-anrop som startar igenkänning.
- Den 1:<sup>a</sup> part klustret returnerar `speech.fragment` meddelanden för mellanliggande resultat, den 3:<sup>e</sup> part returmeddelanden. `speech.hypothesis`