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
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938180"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Förbereda kod för Docker och Kubernetes utveckling
Hittills har du en enkel webbapp som kan köra lokalt. Du kommer nu containerize den genom att skapa tillgångar som definierar appens behållare och hur den ska distribuera till Kubernetes. Den här uppgiften är enkelt att göra med Azure Dev blanksteg: 

1. Starta VS-kod och öppna den `webfrontend` mapp. (Du kan ignorera alla standard frågar om du vill lägga till resurser för felsökning eller återställa projektet.)
1. Öppna integrerad terminalen i VS-kod (med hjälp av den **Visa > integrerad Terminal** menyn).
1. Kör det här kommandot (Kontrollera att **webfrontend** är din aktuella mapp):

    ```cmd
    azds prep --public
    ```

Azure CLI `azds prep` kommandot genererar Docker och Kubernetes tillgångar med standardinställningar:
* `./Dockerfile` Beskriver appens behållaren avbildningen och hur källkoden bygger och kör i behållaren.
* En [Helm diagram](https://docs.helm.sh) under `./charts/webfrontend` beskriver hur du distribuerar behållaren till Kubernetes.

Nu är det inte nödvändigt att förstå det fullständiga innehållet på dessa filer. Det bör pekar på, men som **samma Kubernetes och Docker tillgångar konfigurationskod som kan användas från utveckling till produktion, vilket ger bättre konsekvens mellan olika miljöer.**
 
En fil med namnet `./azds.yaml` också har genererats av den `prep` kommando och det är konfigurationsfilen för Azure Dev blanksteg. Den kompletterar Docker och Kubernetes artefakter med ytterligare konfiguration som gör att en iterativ utvecklingsupplevelse i Azure.
