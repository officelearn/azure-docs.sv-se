---
title: Använda dynamisk konfiguration i en Spring Boot-app
titleSuffix: Azure App Configuration
description: Lär dig hur du dynamiskt uppdaterar konfigurationsdata för Spring Boot-appar
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216763"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Självstudiekurs: Använd dynamisk konfiguration i en Java Spring-app

App Configuration Spring Boot-klientbiblioteket stöder uppdatering av en uppsättning konfigurationsinställningar på begäran, utan att ett program startas om. Klientbiblioteket cachelagrar varje inställning för att undvika för många anrop till konfigurationsarkivet. Uppdateringsåtgärden uppdaterar inte värdet förrän det cachelagrade värdet har upphört att gälla, även när värdet har ändrats i konfigurationsarkivet. Standardtiden för förfallodatum för varje begäran är 30 sekunder. Det kan åsidosättas om det behövs.

Du kan söka efter uppdaterade `AppConfigurationRefresh`inställningar `refreshConfigurations()` på begäran genom att anropa 's metod.

Du kan också använda `spring-cloud-azure-appconfiguration-config-web` paketet, som är `spring-web` beroende av att hantera automatisk uppdatering.

## <a name="use-automated-refresh"></a>Använd automatisk uppdatering

Om du vill använda automatisk uppdatering börjar du med en Spring Boot-app som använder appkonfiguration, till exempel appen du skapar genom att följa [snabbstarten för Spring Boot för appkonfiguration](quickstart-java-spring-app.md).

Öppna sedan *filen pom.xml* i en textredigerare och lägg till en `<dependency>` för `spring-cloud-azure-appconfiguration-config-web`.

**Vårmoln 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Vårmoln 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Spara filen och skapa och kör programmet som vanligt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat appen Spring Boot för att dynamiskt uppdatera konfigurationsinställningarna från AppKonfiguration. Om du vill veta hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till appkonfiguration fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Hanterad identitetsintegrering](./howto-integrate-azure-managed-service-identity.md)
