---
title: Strömma Azure våren Cloud App-loggar i real tid
description: Använda logg strömning för att visa program loggar direkt
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: 27978d367ded7a31d73949cd675ae9e6f8cb887c
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264007"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Strömma Azure våren Cloud App-loggar i real tid
Azure våren Cloud gör det möjligt att logga strömning i Azure CLI för att få real tids program konsol loggar för fel sökning. Du kan också [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md).

## <a name="prerequisites"></a>Krav

* Installera [Azure CLI-tillägget](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli#install-the-azure-cli-extension) för våren Cloud, lägsta version 0.2.0.
* En instans av **Azure våren Cloud** med ett program som körs, till exempel [våren Cloud App](./spring-cloud-quickstart-launch-app-cli.md).

## <a name="use-cli-to-tail-logs"></a>Använd CLI för att logga loggar

Om du vill undvika att flera gånger specificera resurs gruppen och tjänst instans namnet anger du standard resurs grupps namn och kluster namn.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
I följande exempel kommer resurs gruppen och tjänst namnet att utelämnas i kommandona.

### <a name="tail-log-for-app-with-single-instance"></a>Pilslut för app med en enda instans
Om en app som heter auth-service bara har en instans, kan du visa loggen för App-instansen med följande kommando:
```
az spring-cloud app log tail -n auth-service
```
Då returneras loggar:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Pilslut för app med flera instanser
Om det finns flera instanser för appen med namnet `auth-service`kan du Visa instans loggen med hjälp av alternativet `-i/--instance`. 

Först kan du hämta instans namnen för appen med följande kommando.

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
Sedan kan du strömma loggar för en app-instans med alternativet `-i/--instance` alternativ:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Du kan också få information om App-instanser från Azure Portal.  När du har valt **appar** i det vänstra navigerings fönstret i moln tjänsten Azure våren väljer du **App-instanser**.

### <a name="continuously-stream-new-logs"></a>Strömma nya loggar kontinuerligt
Som standard skriver `az spring-cloud ap log tail` bara ut befintliga loggar som strömmas till app-konsolen och sedan avslutas. Om du vill strömma nya loggar lägger du till-f (--följ):  

```
az spring-cloud app log tail -n auth-service -f
``` 
Så här kontrollerar du vilka loggnings alternativ som stöds:
``` 
az spring-cloud app log tail -h 
```

## <a name="next-steps"></a>Nästa steg

* [Analysera loggar och mått med diagnostikinställningar](./diagnostic-services.md)

 





