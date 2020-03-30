---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187757"
---
Jobbet skapar en JSON-utdatafil som innehåller metadata om identifierade och spårade ansikten. Metadata innehåller koordinater som anger platsen för ansikten, samt ett ansikts-ID-nummer som anger spårning av den personen. Face ID-nummer är benägna att återställa under omständigheter när frontytan försvinner eller överlappar i ramen, vilket resulterar i att vissa individer tilldelas flera ID:n.

Utdata JSON innehåller följande element:

### <a name="root-json-elements"></a>Rot JSON-element

| Element | Beskrivning |
| --- | --- |
| version |Detta refererar till versionen av video-API:et. |
| Tidsskalan |"Fästingar" per sekund av videon. |
| offset |Det här är tidsförskjutningen för tidsstämplar. I version 1.0 av Video API:er är detta alltid 0. I framtida scenarier som vi stöder kan det här värdet ändras. |
| bredd, höjd |Bredden och höjden på utdatavideoramen i bildpunkter.|
| Framerate |Bildrutor per sekund i videon. |
| [Fragment](#fragments-json-elements) |Metadata segmenteras i olika segment som kallas fragment. Varje fragment innehåller en start, varaktighet, intervallnummer och händelser. |

### <a name="fragments-json-elements"></a>Fragment JSON element

|Element|Beskrivning|
|---|---|
| start |Starttiden för den första händelsen i "fästingar". |
| varaktighet |Längden på fragmentet, i "fästingar". |
| Index | (Gäller endast Azure Media Redactor) definierar ramindexet för den aktuella händelsen. |
| interval |Intervallet för varje händelsepost i fragmentet, i "fästingar". |
| händelser |Varje händelse innehåller de ansikten som har identifierats och spårats inom den tidsperioden. Det är en rad händelser. Den yttre matrisen representerar ett visst tidsintervall. Den inre matrisen består av 0 eller fler händelser som inträffade vid den tidpunkten. En tom konsol [] betyder att inga ansikten upptäcktes. |
| id |ID:et för ansiktet som spåras. Det här numret kan oavsiktligt ändras om ett ansikte inte har intrecterats. En viss person bör ha samma ID under hela videon, men detta kan inte garanteras på grund av begränsningar i detektionsalgoritmen (ocklusion, etc.). |
| x, y |De övre vänstra X- och Y-koordinaterna för ansiktsgränsningsramen i en normaliserad skala på 0,0 till 1,0. <br/>-X och Y koordinater är i förhållande till landskapet alltid, så om du har ett porträtt video (eller upp och ner, när det gäller iOS), måste du införliva koordinaterna därefter. |
| bredd, höjd |Bredden och höjden på den ansiktsgränsningsramen i en normaliserad skala på 0,0 till 1,0. |
| ansiktenDetected |Detta finns i slutet av JSON resultat och sammanfattar antalet ansikten som algoritmen upptäcktes under videon. Eftersom ID:n kan återställas oavsiktligt om ett ansikte inte har intrecterats (t.ex. går ansiktet utanför skärmen, tittar bort), kanske det här numret inte alltid är lika med det verkliga antalet ansikten i videon. |
