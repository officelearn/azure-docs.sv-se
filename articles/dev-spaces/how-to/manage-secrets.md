---
title: Så här hanterar du hemligheter när du arbetar med ett Azure Dev utrymme | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
ms.technology: azds-kubernetes
description: Snabb utveckling av Kubernetes med behållare och mikrotjänster på Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
manager: douge
ms.openlocfilehash: b77d862f578ddc374dbb58117b4ea58eb973e5fe
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198321"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Så här hanterar du hemligheter när du arbetar med ett utrymme för Azure-utveckling

Dina tjänster kan kräva vissa lösenord, anslutningssträngar och andra hemligheter, t.ex för databaser eller andra säker Azure-tjänster. Genom att ange värden för dessa hemligheter i konfigurationsfilerna, kan du göra dem tillgängliga i koden som miljövariabler.  Dessa måste vara försiktig med att undvika att kompromettera säkerheten för hemligheterna.

Azure Dev blanksteg innehåller två rekommenderat alternativ för lagring av hemligheter: i values.dev.yaml fil- och infogade direkt i azds.yaml. Vi rekommenderar inte för att lagra hemligheter i values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Metod 1: values.dev.yaml
1. Öppna VS kod med ditt projekt som är aktiverad för Azure Dev blanksteg.
2. Lägg till en fil med namnet _values.dev.yaml_ i samma mapp som befintliga _values.yaml_ och definiera din hemliga nyckeln och värden, som i följande exempel:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. Uppdatera _azds.yaml_ som talar om Azure Dev blanksteg till den nya _values.dev.yaml_ fil. Om du vill göra det lägger du till den här konfigurationen under avsnittet configurations.develop.container:

    ```yaml
           container:
             values:
             - "charts/webfrontend/values.dev.yaml"
    ```
 
4. Ändra koden för tjänsten att referera till dessa hemligheter som miljövariabler som i följande exempel:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Uppdatera de tjänster som körs i klustret med de här ändringarna. Kör kommandot på kommandoraden:

    ```
    azds up
    ```
 
6. (Valfritt) Kontrollera att dessa hemligheter har skapats från kommandoraden:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Kontrollera att du lägger till _values.dev.yaml_ till den _.gitignore_ filen för att undvika genomför hemligheter i källkontroll.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metod 2: Infogade direkt i azds.yaml
1.  I _azds.yaml_, ange hemligheter under yaml avsnittet konfigurationer/utveckla/installationen. Även om du kan ange hemlighet värden direkt, inte rekommenderas eftersom _azds.yaml_ checkas in källkontroll. Lägg till platshållarna med syntaxen ”$PLACEHOLDER” i stället.

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT_DEV"
                host: "$REDIS_HOST_DEV"
                key: "$REDIS_KEY_DEV"
    ```
     
2.  Skapa en _.env_ fil i samma mapp som _azds.yaml_. Ange hemligheter med hjälp av standard nyckel = värde-notering. Inte genomföra den _.env_ filen till källkontroll. (Om du vill utesluta från källkontroll i git-baserad version system, lägger du till den i den _.gitignore_ filen.) Följande exempel visar en _.env_ fil:

    ```
    REDIS_PORT_DEV=3333
    REDIS_HOST_DEV=myredishost
    REDIS_KEY_DEV=myrediskey
    ```
2.  Ändringar i källkoden för tjänsten för att referera till dessa hemligheter i koden, som i följande exempel:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Uppdatera de tjänster som körs i klustret med de här ändringarna. Kör kommandot på kommandoraden:

    ```
    azds up
    ```

4.  (valfritt) Visa hemligheter från kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Nästa steg

Med dessa metoder kan du nu på ett säkert sätt ansluta till en databas, ett Redis-cache eller åtkomst säker Azure-tjänster.
 