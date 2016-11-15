---
title: "Program- eller användarspecifik Marathon-tjänst | Microsoft Docs"
description: "Skapa en program- eller användarspecifik Marathon-tjänst"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Behållare, Marathon, Micro-tjänster, DC/OS, Azure"
ms.assetid: 16ecc16e-e504-480e-8dc3-cac14e9e1561
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: befd26888b653c660cc614221684732771496aed


---
# <a name="create-an-application-or-userspecific-marathon-service"></a>Skapa en program- eller användarspecifik Marathon-tjänst
Genom Azure Container Service tillhandahålls en uppsättning huvudservrar där Apache Mesos och Marathon förkonfigureras. Servrarna kan användas för att dirigera dina program i klustret, men det är bäst att inte använda huvudservrarna för det här ändamålet. Om du till exempel vill justera konfigurationen av Marathon måste du logga in på själva huvudservrarna och göra ändringar. Det leder lätt till att du får unika huvudservrar som skiljer sig lite från standarden, vilket betyder att de måste skötas och hanteras var för sig. Dessutom kanske konfigurationen som krävs av ett team inte är den bästa konfigurationen för ett annat team.

I den här artikeln förklarar vi hur du lägger till en användar- eller programspecifik Marathon-tjänst.

Eftersom den här tjänsten tillhör en enskild användare eller ett enskilt team kan den konfigureras på önskat sätt. Azure Container Service säkerställer också att tjänsten fortsätter att köras. Om tjänsten slutar att fungera, startar Azure Container Service om den åt dig. I de flesta fall märker du inte ens att det varit driftstopp.

## <a name="prerequisites"></a>Krav
[Distribuera en instans av Azure Container Service](container-service-deployment.md) med orchestrator-typ DC/OS och [kontrollera att klienten kan ansluta till klustret](container-service-connect.md). Utför följande steg.

[!INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-userspecific-marathon-service"></a>Skapa en program- eller användarspecifik Marathon-tjänst
Börja med att skapa en JSON-konfigurationsfil som definierar namnet på den programtjänst du vill skapa. I det här exemplet använder vi `marathon-alice` som ramverksnamn. Spara filen med något som liknar `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Använd sedan DC/OS CLI för att installera Marathon-instansen med alternativen som anges i konfigurationsfilen:

```bash
dcos package install --options=marathon-alice.json marathon
```

Nu bör du kunna se att tjänsten `marathon-alice` körs på tjänstefliken i DC/OS-gränssnittet. Gränssnittet är `http://<hostname>/service/marathon-alice/` om du vill komma åt det direkt.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Ställa in DC/OS CLI för att komma åt tjänsten
Du kan även konfigurera DC/OS CLI för att komma åt den här nya tjänsten genom att ange att egenskapen `marathon.url` ska peka på instansen `marathon-alice` enligt följande:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Du kan kontrollera vilken instans av Marathon som din CLI arbetar mot med `dcos config show`-kommandot. Du kan återgå till att använda din huvudsakliga Marathon-tjänst med kommandot `dcos config unset marathon.url`.




<!--HONumber=Nov16_HO2-->


