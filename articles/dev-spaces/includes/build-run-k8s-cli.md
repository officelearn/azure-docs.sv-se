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
ms.openlocfilehash: fbbd66dea73747acaf1c267f7d3ba7b1bb17baa2
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36939203"
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

`Service 'webfrontend' port 'http' is available at <url>` 

Öppna webbadressen i ett webbläsarfönster. Du bör nu se hur webbappen läses in. När behållaren körs strömmas `stdout`- och `stderr`-utdata till terminalfönstret.

> [!Note]
> Första gången det körs kan det ta några minuter för DNS att vara redo. Om den offentliga URL:en inte fungerar kan du använda den alternativa http://localhost:<portnumber>-URL:en som visas i konsolens utdata. Om du använder URL:en för localhost kan det verka som om behållaren körs lokalt, men i själva verket körs den i AKS. För enkelhetens skull och för att underlätta interaktionen med tjänsten från den lokala datorn skapar Azure Dev Spaces en tillfällig SSH-tunnel för behållaren som körs i Azure. Du kan komma tillbaka och testa den offentliga URL:en senare när DNS-posten är färdig.
