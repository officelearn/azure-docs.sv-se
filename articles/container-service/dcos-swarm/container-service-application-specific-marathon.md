---
title: FÖRÅLDRAD Program-eller användarspecifik Marathon-tjänst
description: Skapa en program- eller användarspecifik Marathon-tjänst
author: rgardler
ms.service: container-service
ms.topic: conceptual
ms.date: 04/12/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 423dc7f62806f774a5ec4855faa8be9001292773
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277723"
---
# <a name="deprecated-create-an-application-or-user-specific-marathon-service"></a>FÖRÅLDRAD Skapa en program-eller användarspecifik Marathon-tjänst

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Genom Azure Container Service tillhandahålls en uppsättning huvudservrar där Apache Mesos och Marathon förkonfigureras. Servrarna kan användas för att dirigera dina program i klustret, men det är bäst att inte använda huvudservrarna för det här ändamålet. Om du till exempel vill justera konfigurationen av Marathon måste du logga in på själva huvudservrarna och göra ändringar. Det leder lätt till att du får unika huvudservrar som skiljer sig lite från standarden, vilket betyder att de måste skötas och hanteras var för sig. Dessutom kanske konfigurationen som krävs av ett team inte är den bästa konfigurationen för ett annat team.

I den här artikeln förklarar vi hur du lägger till en användar- eller programspecifik Marathon-tjänst.

Eftersom den här tjänsten tillhör en enskild användare eller ett enskilt team kan den konfigureras på önskat sätt. Azure Container Service säkerställer också att tjänsten fortsätter att köras. Om tjänsten slutar att fungera, startar Azure Container Service om den åt dig. I de flesta fall märker du inte ens att det varit driftstopp.

## <a name="prerequisites"></a>Krav
[Distribuera en instans av Azure Container Service](container-service-deployment.md) med Orchestrator-typ DC/OS och [kontrol lera att klienten kan ansluta till klustret](../container-service-connect.md). Utför följande steg.

[!INCLUDE [install the DC/OS CLI](../../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Skapa en program- eller användarspecifik Marathon-tjänst
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

