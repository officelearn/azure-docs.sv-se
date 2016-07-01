<properties
   pageTitle="Program- eller användarspecifik Marathon-tjänst | Microsoft Azure"
   description="Skapa en program- eller användarspecifik Marathon-tjänst"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Marathon, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Skapa en program- eller användarspecifik Marathon-tjänst

Genom Azure Container Service tillhandahålls en uppsättning huvudservrar där Apache Mesos och Marathon förkonfigureras. Servrarna kan användas för att dirigera dina program i klustret, men det är bäst att inte använda huvudservrarna för det här ändamålet. Om du till exempel vill justera konfigurationen av Marathon måste du logga in på själva huvudservrarna och göra ändringar. Det leder lätt till att du får unika huvudservrar som skiljer sig lite från standarden, vilket betyder att de måste skötas och hanteras var för sig. Dessutom kanske konfigurationen som krävs av ett team inte är den bästa konfigurationen för ett annat team. I den här artikeln förklarar vi hur du lägger till en användar- eller programspecifik Marathon-tjänst.

Eftersom den här tjänsten tillhör en enskild användare eller ett enskilt team kan den konfigureras på önskat sätt. Dessutom säkerställer Azure Container Service att tjänsten fortsätter att köras. Om det skulle bli avbrott i tjänsten startar Azure Container Service om den. I de flesta fall märker du inte ens att det varit driftstopp.

## Krav

[Distribuera en Azure Container Service-instans](container-service-deployment.md) med orchestrator-typ DC/OS, [kontrollera att klienten kan ansluta till klustret](container-service-connect.md) och [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].

## Skapa en program- eller användarspecifik Marathon-tjänst.

Börja med att skapa en JSON-konfigurationsfil som definierar namnet på den programtjänst du vill skapa. I det här exemplet använder vi `marathon-alice` som ramverksnamn. Spara filen med något som liknar `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Använd sedan DC/OS CLI för att installera Marathon-instansen med alternativen som anges i konfigurationsfilen:

```bash
dcos package install --options=marathon-alice.json marathon
```

Nu bör du kunna se att tjänsten `marathon-alice` körs på tjänstefliken i DC/OS-gränssnittet. Gränssnittet är `http://<hostname>/service/marathon-alice/` om du vill komma åt det direkt.

## Ställa in DC/OS CLI för att komma åt tjänsten

Du kan även konfigurera DC/OS CLI för att komma åt den här nya tjänsten genom att ange att egenskapen `marathon.url` ska peka på instansen `marathon-alice` enligt följande:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Du kan bekräfta vilken Marathon-instans som CLI fungerar med genom att ange kommandot `dcos config show`, och du kan återgå till att använda Marathon-huvudtjänsten med kommandot `dcos config unset marathon.url`.


<!--HONumber=Jun16_HO2-->


