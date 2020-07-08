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
ms.openlocfilehash: eb31fa271496b0eeedf4a2b845ab05e5f241e167
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041990"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Ange cookie-principen för den fördjupade läsaren

Den fördjupade läsaren kommer att inaktivera cookie-användning som standard. Om du aktiverar cookie-användning kan den fördjupade läsaren använda cookies för att underhålla användar inställningar och spåra funktions användning. Om du aktiverar användning av cookies i den fördjupade läsaren bör du ta hänsyn till kraven i EU: s policy för cookie-efterlevnad. Det är värd programmets ansvar för att erhålla nödvändiga användar medgivande i enlighet med EU: s policy för cookie-kompatibilitet.

Cookie-principen kan ställas in via [alternativen](../reference.md#options)för avancerad läsare.

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

* Visa [Node.js snabb start](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av Node.js
* Visa [Android-självstudien](../tutorial-android.md) för att se vad mer du kan göra med SDK: n för avancerad läsare med Java eller Kotlin för Android
* Visa [iOS-självstudien](../tutorial-ios.md) för att se vad mer du kan göra med SDK: n för avancerad läsare med Swift för iOS
* Visa [python-självstudien](../tutorial-python.md) för att se vad mer du kan göra med avancerad läsar-SDK med hjälp av python
* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](../reference.md)