---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 5b1b361778de145a5e32a07bb0164ff2293d9a1a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279476"
---
I avsnittet **Hantera** (övre högra menyn) på sidan **Azure Resources** (vänster-menyn) kopierar du **URL:en exempelfråga** och klistrar sedan in på en ny webbläsarflik.

Slutpunkts-URL:en ser ut som följande format, med ditt eget app-ID och slutpunktsnyckel som ersätter APP-ID och NYCKEL-ID:

```console
https://westus.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```