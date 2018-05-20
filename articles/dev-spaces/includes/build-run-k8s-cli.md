---
title: ta med fil
description: ta med fil
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 1cf301ab3b4430c7bb79f6fdd72243f9f2ad1d9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes
Vi kör vår kod! I fönstret terminal kör du kommandot från den **kod rotmappen**, webfrontend:

```cmd
azds up
```

Hålla ett öga på kommandots utdata, Lägg märke till flera saker när den:
- Källkoden har synkroniserats till utvecklingsmiljö i Azure.
- En avbildning av behållare är inbyggd i Azure, som anges av Docker tillgångar i mappen kod.
- Kubernetes objekt skapas som använder behållaren avbildningen som anges av Helm diagram i mappen kod.
- Information om behållarens-slutpunkt(er) visas. I det här fallet det förväntas en offentlig HTTPS-URL.
- Under förutsättning att ovanstående steg slutföras snart bör du se `stdout` (och `stderr`) utdata som behållaren startas.

> [!Note]
> De här stegen tar längre tid första gången de `up` kommandot körs, men efterföljande körs ska gå snabbare.

## <a name="test-the-web-app"></a>Testa webbappen
Skanna konsolens utdata för information om offentlig URL: en som skapades av den `up` kommando. Det ska vara i formatet: 

`Running at public URL: https://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

Öppna URL: en i ett webbläsarfönster och du bör se webbappen att läsa in. När behållaren kör `stdout` och `stderr` utdata strömmas till fönstret terminal.
