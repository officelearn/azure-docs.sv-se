---
title: Strömma apploggar för Azure Spring Cloud i realtid
description: Så här använder du loggströmning för att visa programloggar direkt
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fc208a3542528fb4554a365a02e13c2da3055cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192208"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Strömma apploggar för Azure Spring Cloud i realtid
Azure Spring Cloud gör det möjligt att logga direktuppspelning i Azure CLI för att få programkonsolloggar i realtid för felsökning. Du kan också [analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md).

## <a name="prerequisites"></a>Krav

* Installera [Azure CLI-tillägg](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) för Spring Cloud, minsta version 0.2.0 .
* En instans av **Azure Spring Cloud** med ett program som körs, till exempel Spring [Cloud-appen](./spring-cloud-quickstart-launch-app-cli.md).

> [!NOTE]
>  ASC CLI-tillägget uppdateras från version 0.2.0 till 0.2.1. Den här ändringen påverkar syntaxen för `az spring-cloud app log tail`kommandot för loggströmning: , som ersätts med: `az spring-cloud app logs`. Kommandot: `az spring-cloud app log tail` kommer att inaktuell i en framtida version. Om du har använt version 0.2.0 kan du uppgradera till 0.2.1. Ta först bort den gamla `az extension remove -n spring-cloud`versionen med kommandot: .  Installera sedan 0.2.1 med `az extension add -n spring-cloud`kommandot: .

## <a name="use-cli-to-tail-logs"></a>Använd CLI för att svansa stockar

Om du vill undvika att upprepade gånger ange resursgrupps- och tjänstinstansnamnet anger du standardnamn och klusternamn för resursgruppen.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
I följande exempel utelämnas resursgruppen och tjänstnamnet i kommandona.

### <a name="tail-log-for-app-with-single-instance"></a>Baklogg för app med en instans
Om en app med namnet auth-service bara har en instans kan du visa loggen för appinstansen med följande kommando:
```
az spring-cloud app logs -n auth-service
```
Detta returnerar loggar:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Tail log för app med flera instanser
Om det finns flera instanser för appen med namnet `auth-service` `-i/--instance` kan du visa instansloggen med hjälp av alternativet. 

Först kan du hämta appinstansnamnen med följande kommando.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Med resultat:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Sedan kan du strömma loggar för en `-i/--instance` appinstans med alternativet:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Du kan också få information om appinstanser från Azure-portalen.  När du har valt **Appar** i det vänstra navigeringsfönstret i azure spring cloud-tjänsten väljer du **Appinstanser**.

### <a name="continuously-stream-new-logs"></a>Strömma kontinuerligt nya loggar
Som standard `az spring-cloud ap log tail` skrivs bara befintliga loggar som strömmas till appkonsolen och avslutas sedan. Om du vill strömma nya loggar lägger du till -f (--följ):  

```
az spring-cloud app logs -n auth-service -f
``` 
Så här kontrollerar du alla loggningsalternativ som stöds:
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Nästa steg

* [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md)

 





