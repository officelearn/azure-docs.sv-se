---
title: Ange cookie-princip för avancerad läsare
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du ställer in cookie-principen för den fördjupade läsaren.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946115"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Ange cookie-principen för den fördjupade läsaren

Den fördjupade läsaren kommer att inaktivera cookie-användning som standard. Om du aktiverar cookie-användning kan den fördjupade läsaren använda cookies för att underhålla användar inställningar och spåra funktions användning. Om du aktiverar användning av cookies i den fördjupade läsaren bör du ta hänsyn till kraven i EU: s policy för cookie-efterlevnad. Det är värd programmets ansvar för att erhålla nödvändiga användar medgivande i enlighet med EU: s policy för cookie-kompatibilitet.

Cookie-principen kan ställas in via [alternativen](../reference.md#options)för avancerad läsare. Mer information finns i [CookiePolicy Enum](../reference.md#cookiepolicy-enum) .

## <a name="enable-cookie-usage"></a>Aktivera användning av cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Inaktivera cookie-användning

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Nästa steg

* Visa snabb starten för [Node. js](../quickstart-nodejs.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av Node. js
* Visa [python-självstudien](../tutorial-python.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av python
* Se snabb [självstudien](../tutorial-ios-picture-immersive-reader.md) för att se vad mer du kan göra med avancerad läsar-SDK med Swift
* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../reference.md)