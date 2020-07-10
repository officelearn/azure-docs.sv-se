---
title: Miljövariabler
description: Ange miljövariabler
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156665"
---
Med din nyckel och slut punkt från den resurs som du har skapat skapar du två miljövariabler för autentisering:

* `FORM_RECOGNIZER_KEY`– Resurs nyckeln för autentisering av dina begär Anden.
* `FORM_RECOGNIZER_ENDPOINT`– Resurs slut punkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> Slut punkterna för resurser som skapats efter den 1 juli 2019 använder det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Använd följande instruktioner för att ställa in miljövariabler i operativ systemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

När du har lagt till miljövariablerna stänger du och öppnar konsol fönstret igen.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[macOS](#tab/unix)

Redigera din `.bash_profile` och Lägg till miljövariabeln:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

När du har lagt till miljövariablerna kör du `source .bash_profile` från konsol fönstret för att ändringarna ska börja gälla.
***
