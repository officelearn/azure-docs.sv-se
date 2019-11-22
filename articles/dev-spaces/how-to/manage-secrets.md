---
title: Så här hanterar du hemligheter när du arbetar med ett Azure dev-utrymme
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.openlocfilehash: 49f53683b2499e790414d139dcb0bc0833005647
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280010"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Så här hanterar du hemligheter när du arbetar med ett Azure dev-utrymme

Dina tjänster kan kräva vissa lösen ord, anslutnings strängar och andra hemligheter, t. ex. för databaser eller andra säkra Azure-tjänster. Genom att ange värden för dessa hemligheter i konfigurationsfiler kan du göra dem tillgängliga i koden som miljövariabler.  Dessa måste hanteras med försiktighet för att undvika att Hemligheternas säkerhet äventyras.

Azure dev Spaces innehåller två rekommenderade, strömlinjeformade alternativ för att lagra hemligheter i Helm-diagram som genereras av Azure dev Spaces-klient verktyg: i filen Values. dev. yaml och infogas direkt i azds. yaml. Vi rekommenderar inte att du lagrar hemligheter i Values. yaml. Utanför de två metoderna för Helm-diagram som genereras av klient verktyg som definieras i den här artikeln, om du skapar ett eget Helm-diagram kan du använda Helm-diagrammet direkt för att hantera och lagra hemligheter.

## <a name="method-1-valuesdevyaml"></a>Metod 1: values. dev. yaml
1. Öppna VS Code med projektet som är aktiverat för Azure dev Spaces.
2. Lägg till en fil med namnet _Values. dev. yaml_ i samma mapp som befintlig _azds. yaml_ och definiera din hemliga nyckel och dina värden, som i följande exempel:

    ```yaml
    secrets:
      redis:
        port: "6380"
        host: "contosodevredis.redis.cache.windows.net"
        key: "secretkeyhere"
    ```
     
3. _azds. yaml_ refererar redan till filen _Values. dev. yaml_ om den finns. Om du föredrar ett annat fil namn uppdaterar du avsnittet install. Values:

    ```yaml
    install:
      values:
      - values.dev.yaml?
      - secrets.dev.yaml?
    ```
 
4. Ändra tjänst koden så att den refererar till dessa hemligheter som miljövariabler, som i följande exempel:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
    
5. Uppdatera de tjänster som körs i klustret med dessa ändringar. Kör kommandot på kommando raden:

    ```
    azds up
    ```
 
6. Valfritt På kommando raden kontrollerar du att dessa hemligheter har skapats:

      ```
      kubectl get secret --namespace default -o yaml 
      ```

7. Se till att du lägger till _Values. dev. yaml_ i _. gitignore_ -filen för att undvika att bekräfta hemligheter i käll kontrollen.
 
 
## <a name="method-2-inline-directly-in-azdsyaml"></a>Metod 2: Infoga direkt i azds. yaml
1.  I _azds. yaml_anger du hemligheter under yaml-avsnittet konfigurationer/utveckla/installera. Du kan ange hemliga värden direkt där, men det rekommenderas inte eftersom _azds. yaml_ har checkats in i käll kontrollen. Lägg i stället till plats hållare med "$PLACEHOLDER"-syntaxen.

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
     
2.  Skapa en _. kuvert_ -fil i samma mapp som _azds. yaml_. Ange hemligheter med standard nyckel = värde notation. Spara inte _. kuvert_ -filen i käll kontrollen. (Om du vill utesluta från käll kontroll i git-baserade versions kontroll system lägger du till den i _. gitignore_ -filen.) I följande exempel visas en _. miljö_ -fil:

    ```
    REDIS_PORT=3333
    REDIS_HOST=myredishost
    REDIS_KEY=myrediskey
    ```
2.  Ändra tjänst käll koden för att referera till dessa hemligheter i kod, som i följande exempel:

    ```
    var redisPort = process.env.REDIS_PORT
    var host = process.env.REDIS_HOST
    var theKey = process.env.REDIS_KEY
    ```
 
3.  Uppdatera de tjänster som körs i klustret med dessa ändringar. Kör kommandot på kommando raden:

    ```
    azds up
    ```

4.  valfritt Visa hemligheter från kubectl:

    ```
    kubectl get secret --namespace default -o yaml
    ```

## <a name="next-steps"></a>Nästa steg

Med dessa metoder kan du på ett säkert sätt ansluta till en databas, en Azure-cache för Redis eller komma åt säkra Azure-tjänster.
 
