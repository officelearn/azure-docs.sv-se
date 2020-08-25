---
title: inkludera fil
description: inkludera fil
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "87375195"
---
I avsnittet **Hantera** (övre högra menyn) på sidan **Azure-resurser** (den vänstra menyn) kopierar du **frågans** URL och klistrar in den i en ny flik i webbläsaren.

Slut punkts-URL: en ser ut som i följande format, med din egen anpassade under domän, app-ID och slut punkts nyckel som ersätter APP-ID och nyckel-ID:

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```