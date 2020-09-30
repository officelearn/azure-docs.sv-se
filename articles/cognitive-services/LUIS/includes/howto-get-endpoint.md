---
title: ta med fil
description: ta med fil
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544971"
---
I avsnittet **Hantera** (övre högra menyn) på sidan **Azure-resurser** (den vänstra menyn) kopierar du **frågans** URL och klistrar in den i en ny flik i webbläsaren.

Slut punkts-URL: en ser ut som i följande format, med din egen anpassade under domän, app-ID och slut punkts nyckel som ersätter APP-ID och nyckel-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```