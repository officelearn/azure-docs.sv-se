---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "81422104"
---
## <a name="speech-modes"></a>Tallägen

**Ej**
- Avsedd för kommando-och kontroll scenarier.
- Har ett timeout-värde för segmentering på X.
- I slutet av en känd uttryck slutar tjänsten att bearbeta ljud från det begär ande-ID: t och avslutar turn. Anslutningen är inte stängd.
- Max gränsen för igenkänning är 20s.
- Ett typiskt kol samtal till Invoke är `RecognizeOnceAsync` .

**Konversation**
- Avsedd för längre körning av igenkänning.
- Har ett timeout-värde för segmentering av Y. (Y! = X)
- Kommer att bearbeta flera fullständiga yttranden utan att avsluta turn.
- Avslutar inaktive ras för hög tystnad.
- Kol kommer att fortsätta med ett nytt ID för begäran och spela upp ljud efter behov.
- Tjänsten tvingas att koppla från efter 10 minuter tal igenkänning.
- Kol kommer att återansluta och spela upp ej godkänt ljud.
- Anropades under kol with `StartContinuousRecognition` .

**Diktering**
- Tillåter användare att ange interpunktion genom att tala.
- Anropas i kol genom `EnableDictation` att ange på `SpeechConfig` objektet oavsett vilket API-anrop som påbörjar igenkänningen.
- 1<sup>St</sup> part-klustret returnerar `speech.fragment` meddelanden om mellanliggande resultat, och de tre<sup>RD</sup> -parternas retur `speech.hypothesis` meddelanden.