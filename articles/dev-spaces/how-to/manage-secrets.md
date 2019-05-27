---
title: Hantera hemligheter när du arbetar med ett adressutrymme för Azure-utveckling
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 8ee50289083b12b7b2abd3b9ece2c8de345df9fe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851439"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Hantera hemligheter när du arbetar med ett adressutrymme för Azure-utveckling

Dina tjänster kan kräva viss lösenord, anslutningssträngar och andra hemligheter som för databaser eller andra säkra Azure-tjänster. Genom att ange värdena för dessa hemligheter i konfigurationsfiler, kan du göra dem tillgängliga i din kod som miljövariabler.  Dessa måste vara försiktig med att undvika att kompromettera säkerheten för hemligheterna.

Azure Dev blanksteg innehåller två rekommenderade alternativ för att lagra hemligheter: i values.dev.yaml fil- och infogade direkt i azds.yaml. Vi rekommenderar inte för att lagra hemligheter i values.yaml.
 
## <a name="method-1-valuesdevyaml"></a>Metod 1: values.dev.yaml
1. Öppna VS Code med ditt projekt som är aktiverad för Azure Dev blanksteg.
2. Lägg till en fil med namnet _values.dev.yaml_ i samma mapp som befintliga _azds.yaml_ och definiera hemlig nyckel och värden, som i följande exempel:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds.yaml_ redan till den _values.dev.yaml_ filen om den finns. Om du föredrar ett annat namn kan du uppdatera avsnittet install.values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Ändra koden för tjänsten att referera till dessa hemligheter som miljövariabler, som i följande exempel:

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

7. Se till att du lägger till _values.dev.yaml_ till den _.gitignore_ fil för att undvika transaktionen hemligheter i källkontrollen.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metod 2: Infogat direkt i azds.yaml
1.  I _azds.yaml_, ange hemligheter under yaml avsnittet konfigurationer/utveckla/installationen. Men du kan ange hemlighet värden direkt där det inte rekommenderas eftersom _azds.yaml_ kontrolleras i källkontrollen. Lägg till platshållare med hjälp av ”$PLACEHOLDER”-syntax i stället.

    ```yaml
    configurations:
      develop:
        ...
        install:
          set:
            secrets:
              redis:
                port: "$REDIS_PORT"
                host: "$REDIS_HOST"
                key: "$REDIS_KEY"
    ```
     
2.  Skapa en _.env_ fil i samma mapp som _azds.yaml_. Ange hemligheter med hjälp av standard nyckel = värde-notering. Inte genomföra den _.env_ filen till källkontroll. (Om du vill utelämna från källkontroll i git-baserade versionskontrollsystem, lägger du till den i den _.gitignore_ fil.) I följande exempel visas en _.env_ fil:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Ändra service källkoden för att referera till dessa hemligheter i kod, som i följande exempel:

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

Med dessa metoder kan du nu på ett säkert sätt ansluta till en databas, Azure Cache för Redis, eller komma åt säker Azure-tjänster.
 
