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
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198834"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Förbereda kod för Docker och Kubernetes utveckling
Hittills har du en enkel webbapp som kan köra lokalt. Du kommer nu containerize den genom att skapa tillgångar som definierar appens behållare och hur den ska distribuera till Kubernetes. Det är lätt att göra med Azure Dev blanksteg: 

1. Starta VS-kod och öppna den `webfrontend` mapp. (Du kan ignorera alla standard frågar om du vill lägga till resurser för felsökning eller återställa projektet.)
1. Öppna integrerad terminalen i VS-kod (med hjälp av den **Visa > integrerad Terminal** menyn).
1. Kör det här kommandot (Kontrollera att **webfrontend** är din aktuella mapp):

    ```cmd
    azds prep --public
    ```

Azure CLI `azds prep` kommandot genererar Docker och Kubernetes tillgångar med standardinställningar:
* `./Dockerfile` Beskriver appens behållaren avbildningen och hur källkoden bygger och kör i behållaren.
* En [Helm diagram](https://docs.helm.sh) under `./charts/webfrontend` beskriver hur du distribuerar behållaren till Kubernetes.

Nu är det nödvändigt att förstå det fullständiga innehållet på dessa filer. Det bör pekar på, men som **samma Kubernetes och Docker tillgångar konfigurationskod som kan användas från utveckling till produktion, vilket ger bättre konsekvens mellan olika miljöer.**
 
En fil med namnet `./azds.yaml` också har genererats av den `prep` kommando och det är konfigurationsfilen för Azure Dev blanksteg. Den kompletterar Docker och Kubernetes artefakter med ytterligare konfiguration som gör att en iterativ utvecklingsupplevelse i Azure. Standard Helm diagrammet visar till exempel inte någon offentlig slutpunkt. Ibland, men är den användbar för att tillfälligt öppna upp en offentlig slutpunkt under utvecklingen så att du kan testa din kod säger, från en mobil enhet eller en Webhooksadressen. En azds.yaml-fil som skapats med hjälp av `azds prep --public` åsidosätter Helm standardparametrar för att exponera en offentlig slutpunkt för endast tid.
