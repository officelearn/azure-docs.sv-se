---
title: Distribuera Azure API Management-Gateway med egen värd till Docker | Microsoft Docs
description: Lär dig hur du distribuerar en Azure API Management-Gateway med egen värd till Docker
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
ms.openlocfilehash: bfb012116f74e451f9479c8c65057e5c8405bbb4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513788"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuera en Azure API Management egen värd-Gateway till Docker

Den här artikeln innehåller anvisningar för att distribuera Azure API Management Gateway i egen värd till en Docker-miljö.

> [!NOTE]
> Funktionen för lokal gateway är i för hands version. Under för hands versionen är den egna värdbaserade gatewayen bara tillgänglig på utvecklings-och Premium-nivåerna utan extra kostnad. Developer-nivån är begränsad till en enda lokal gateway-distribution.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md)
- Skapa en Docker-miljö. [Docker för skriv bord](https://www.docker.com/products/docker-desktop) är ett utmärkt alternativ för utveckling och utvärdering. Se [Docker-dokumentationen](https://docs.docker.com) för information om alla Docker-versioner, deras funktioner och omfattande dokumentation på Docker.
- [Etablera en gateway-resurs i API Management-instansen](api-management-howto-provision-self-hosted-gateway.md)

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuera den egna värdbaserade gatewayen till Docker

1. Välj **gateways** från **Inställningar**.
2. Välj den gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. Observera att en ny token i text rutan **token** genererades automatiskt när du använder standard värdena för **förfallo datum** och **hemlig nyckel** . Justera antingen eller båda om du vill och välj **generera** för att skapa en ny token.
4. Kontrol lera att **Docker** är markerat under **distributions skript**.
5. Klicka på **kuvert. conf** -fillänk bredvid **miljön** för att ladda ned filen.
6. Välj **Kopiera** -ikonen längst till höger i text rutan **Kör** för att spara Docker-kommandot i Urklipp.
7. Klistra in kommandot i terminalfönstret (eller kommando fönstret). Justera port mappningarna och behållar namnet efter behov. Observera att kommandot förväntar sig att den nedladdade miljö filen finns i den aktuella katalogen.
```
    docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
```
8. Kör kommandot. Kommandot instruerar din Docker-miljö att köra behållaren med hjälp av den lokala gatewayens avbildning som hämtats från Microsoft Container Registry och för att mappa behållarens HTTP-(8080)-och HTTPS-portar (8081) till portarna 80 och 443 på värden.
9. Kör kommandot nedan för att kontrol lera att Gateway-Pod körs:
```console
docker ps
CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:beta   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
```
10. Gå tillbaka till Azure Portal och kontrol lera att Gateway-noden som du precis har distribuerat rapporterar felfri status.

![Gateway-status](media/api-management-howto-deploy-self-hosted-gateway-to-docker/status.png)

> [!TIP]
> Använd <code>console docker container logs <gateway-name></code> kommando om du vill visa en ögonblicks bild av Gateway-loggen med egen värd.
>
> Använd <code>docker container logs --help</code> kommando för att visa alla logg visnings alternativ.

## <a name="next-steps"></a>Nästa steg

* Mer information om den egen värdbaserade gatewayen finns i [Översikt över Azure API Management egen Gateway](self-hosted-gateway-overview.md)
