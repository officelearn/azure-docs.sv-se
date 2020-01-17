---
title: Strömma Azure våren Cloud App-loggar i real tid
description: Använda logg strömning för att visa program loggar direkt
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.openlocfilehash: fa2e7af51ff681da0bfdac928cc08bf75126a3b8
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156428"
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
Om det finns flera instanser för appen med namnet `auth-service`kan du Visa instans loggen med hjälp av alternativet `-i/--instance`. Du kan till exempel strömma loggen för en instans av en app genom att ange appens namn och instans namn:

```
az spring-cloud app log tail -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```
Du kan också hämta app-instanserna från Azure Portal. 
1. Navigera till din resurs grupp och välj din Azure våren Cloud-instans.
1. Från översikt över Azure våren Cloud instance väljer du **appar** i det vänstra navigerings fönstret.
1. Välj din app och klicka sedan på **App-instanser** i det vänstra navigerings fönstret. 
1. App-instanser visas.

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

 





