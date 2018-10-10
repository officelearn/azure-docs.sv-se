---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904529"
---
<a name="paths"></a>
## <a name="paths"></a>Sökvägar

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Identifiera avvikelser punkter för den tid som datapunkter i serien begärdes
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametrar

|Typ|Namn|Beskrivning|Schema|
|---|---|---|---|
|**Brödtext**|**Brödtext**  <br>*Krävs*|Tidsserien datapunkter och tid om det behövs.|[Begäran](#request)|


#### <a name="responses"></a>Svar

|HTTP-kod|Beskrivning|Schema|
|---|---|---|
|**200**|Åtgärden lyckas.|< [svaret](#response) > matris|
|**400**|Går inte att parsa JSON-förfrågan.|Inget innehåll|
|**403**|Det certifikat du angett accepteras inte av servern.|Inget innehåll|
|**405**|Metoden tillåts inte.|Inget innehåll|
|**500**|Internt serverfel.|Inget innehåll|


#### <a name="consumes"></a>Förbrukar

* `application/json`


#### <a name="produces"></a>Genererar

* `application/json`


#### <a name="tags"></a>Taggar

* anomalydetection



