---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353331"
---
<a name="paths"></a>
## <a name="paths"></a>Sökvägar

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Identifiera avvikelseidentifiering punkter gången datapunkter i serien begärdes
```
POST /anomalydetection
```


#### <a name="parameters"></a>Parametrar

|Typ|Namn|Beskrivning|Schema|
|---|---|---|---|
|**Brödtext**|**Brödtext**  <br>*Krävs*|Tidsserier datapunkter och tid om det behövs.|[Begäran](#request)|


#### <a name="responses"></a>Svar

|HTTP-kod|Beskrivning|Schema|
|---|---|---|
|**200**|Åtgärden lyckades.|< [svaret](#response) > matris|
|**400**|Det går inte att tolka JSON-förfrågan.|Inget innehåll|
|**403**|Det certifikat som du har angetts accepteras inte av servern.|Inget innehåll|
|**405**|Metoden är inte tillåten.|Inget innehåll|
|**500**|Internt serverfel.|Inget innehåll|


#### <a name="consumes"></a>Förbrukar

* `application/json`


#### <a name="produces"></a>Genererar

* `application/json`


#### <a name="tags"></a>Taggar

* anomalydetection



