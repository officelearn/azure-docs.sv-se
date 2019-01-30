---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228911"
---
<a name="paths"></a>
## <a name="paths"></a>Sökvägar

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Identifiera avvikelser punkter för den tid som datapunkter i serien begärdes
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametrar

|Type|Name|Beskrivning|Schema|
|---|---|---|---|
|**Brödtext**|**body**  <br>*Krävs*|Tidsserien datapunkter och tid om det behövs.|[request](#request)|


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



