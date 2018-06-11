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
ms.openlocfilehash: 484567dd9d9c3d050e7be25bd685a5b8d3de0687
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34825553"
---
## <a name="build-and-run-code-in-kubernetes"></a>Skapa och köra kod i Kubernetes
Nu kör vi vår kod! Kör det här kommandot från webbklientdelen **rotmappen för kod** i terminalfönstret:

```cmd
azds up
```

Håll ett öga på kommandots utdata – du kommer att se flera saker under förloppet:
- Källkoden synkroniseras till utvecklarmiljön i Azure.
- En behållaravbildning skapas i Azure, enligt Docker-tillgångarna i kodmappen.
- Kubernetes-objekt som använder behållaravbildningen enligt Helm-diagrammet i kodmappen skapas.
- Information om behållarens slutpunkt(er) visas. I det här fallet förväntar vi oss en offentlig HTTP-URL.
- Förutsatt att ovanstående steg slutförs ordentligt kan du börja se `stdout`- (och `stderr`-) utdata när behållaren startas.

> [!Note]
> De här stegen tar längre tid första gången kommandot `up` körs, men efterföljande körningar bör gå snabbare.

### <a name="test-the-web-app"></a>Testa webbappen
Skanna konsolens utdata för information om den offentliga webbadressen som skapades av kommandot `up`. Den kommer att vara i formen: 

`Running at public URL: http://<servicename>-<cluster-name>.<guid>.<region>.aksapp.io` 

Öppna webbadressen i ett webbläsarfönster. Du bör nu se hur webbappen läses in. När behållaren körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.
