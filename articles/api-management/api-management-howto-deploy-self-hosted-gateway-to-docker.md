---
title: Distribuera självvärd azure API Management-gateway till Docker | Microsoft-dokument
description: Lär dig hur du distribuerar en självvärd Azure API Management-gateway till Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: b5acda30f7f4a01e1b7b6ac82652452c3c40e3bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768511"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuera en självvärd port för Azure API Management till Docker

Den här artikeln innehåller stegen för att distribuera självvärd Azure API Management-gateway till en Docker-miljö.

> [!NOTE]
> Den självvärdbaserade gatewayfunktionen är i förhandsversion. Under förhandsversionen är den självvärdbaserade gatewayen endast tillgänglig på utvecklar- och premiumnivåerna utan extra kostnad. Utvecklarnivån är begränsad till en enda självvärd gateway-distribution.

## <a name="prerequisites"></a>Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa en Docker-miljö. [Docker for Desktop](https://www.docker.com/products/docker-desktop) är ett bra alternativ för utvecklings- och utvärderingsändamål. Se [Docker-dokumentation](https://docs.docker.com) för information om alla Docker-utgåvor, deras funktioner och omfattande dokumentation om Docker själv.
- [Etablera en gateway-resurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Självvärdbaserad gateway är paketerad som en x86-64 Linux-baserad Docker-behållare.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuera den självvärderade gatewayen till Docker

1. Välj **Gateways** under **Inställningar**.
2. Välj den gatewayresurs som du tänker distribuera.
3. Välj **Distribution**.
4. Observera att en ny token i textrutan **Token** skapades automatiskt med standardvärdena **Förfallodatum** och **hemlig nyckel.** Justera antingen eller båda om så önskas och välj **Generera** för att skapa en ny token.
4. Kontrollera att **Docker** är markerat under **Distributionsskript**.
5. Välj **env.conf** fillänk bredvid **miljön** för att hämta filen.
6. Välj **kopieringsikon** som finns till höger i textrutan **Kör** om du vill spara Kommandot Docker i Urklipp.
7. Klistra in kommandot i terminalfönstret (eller kommandofönstret). Justera portmappningarna och behållarnamnet efter behov. Observera att kommandot förväntar sig att den hämtade miljöfilen ska finnas i den aktuella katalogen.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Kör kommandot. Kommandot instruerar Docker-miljön att köra behållaren med hjälp av självvärderade gateway-avbildningar som hämtats från Microsoft Container-registret och att mappa behållarens HTTP-portar (8080) och HTTPS (8081) till portarna 80 och 443 på värden.
9. Kör kommandot nedan för att kontrollera att gateway pod körs:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Gå tillbaka till Azure-portalen och bekräfta att gateway-nod som du just har distribuerat rapporterar felfritt.

![gateway-status](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Använd <code>console docker container logs <gateway-name></code> kommandot för att visa en ögonblicksbild av självvärdbaserad gatewaylogg.
>
> Använd <code>docker container logs --help</code> kommandot för att se alla loggvisningsalternativ.

## <a name="next-steps"></a>Nästa steg

* Mer information om den självvärderade gatewayen finns i [Azure API Management självvärderade gateway översikt](self-hosted-gateway-overview.md).
* [Konfigurera anpassat domännamn för den självvärdbaserade gatewayen](api-management-howto-configure-custom-domain-gateway.md).
