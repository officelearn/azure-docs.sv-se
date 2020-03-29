---
title: Ange immersive Reader Cookie Policy
titleSuffix: Azure Cognitive Services
description: Den här artikeln visar hur du ställer in cookiepolicyn för Immersive Reader.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946115"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Så här ställer du in cookiepolicyn för Immersive Reader

Den uppslukande läsaren inaktiverar cookie-användning som standard. Om du aktiverar cookieanvändning kan Immersive Reader använda cookies för att upprätthålla användarinställningar och spåra användning av funktioner. Om du aktiverar cookieanvändning i Immersive Reader bör du överväga kraven i EU:s policy för efterlevnad av cookies. Det är värdprogrammets ansvar att inhämta alla nödvändiga användarmedgivanden i enlighet med EU:s policy för efterlevnad av cookies.

Cookie-policyn kan ställas in via [alternativen](../reference.md#options)för Immersive Reader . Se [CookiePolicy-uppräkningen](../reference.md#cookiepolicy-enum) för mer information.

## <a name="enable-cookie-usage"></a>Aktivera cookieanvändning

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Inaktivera cookieanvändning

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Nästa steg

* Visa [snabbstarten Node.js](../quickstart-nodejs.md) för att se vad mer du kan göra med Immersive Reader SDK med Node.js
* Visa [Python-självstudien](../tutorial-python.md) för att se vad mer du kan göra med Immersive Reader SDK med Python
* Visa [Swift-självstudien](../tutorial-ios-picture-immersive-reader.md) för att se vad mer du kan göra med Immersive Reader SDK med Swift
* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](../reference.md)