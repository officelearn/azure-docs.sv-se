---
title: Använd dynamisk konfiguration i en våren Boot-app
titleSuffix: Azure App Configuration
description: Lär dig hur du dynamiskt uppdaterar konfigurations data för våren Boot Apps
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 3/5/2020
ms.author: lcozzens
ms.openlocfilehash: 37c832e3b6d1430da0b45558c9632f0486a7233b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79216763"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Självstudie: Använd dynamisk konfiguration i en Java våren-app

App-konfigurationen för att starta klient biblioteket stöder uppdatering av en uppsättning konfigurations inställningar på begäran, utan att ett program startas om. Klient biblioteket cachelagrar varje inställning för att undvika för många anrop till konfigurations arkivet. Uppdaterings åtgärden uppdaterar inte värdet förrän det cachelagrade värdet har upphört att gälla, även om värdet har ändrats i konfigurations arkivet. Standard förfallo tiden för varje begäran är 30 sekunder. Den kan åsidosättas om det behövs.

Du kan kontrol lera om det finns uppdaterade inställningar på `AppConfigurationRefresh`begäran `refreshConfigurations()` genom att anropa metoden.

Du kan också använda `spring-cloud-azure-appconfiguration-config-web` paketet, som tar ett beroende på `spring-web` för att hantera automatisk uppdatering.

## <a name="use-automated-refresh"></a>Använd automatisk uppdatering

Om du vill använda automatisk uppdatering börjar du med en våren Boot-app som använder appens konfiguration, till exempel den app som du skapar genom att följa [Start programmet för att starta snabb start för appar](quickstart-java-spring-app.md).

Öppna sedan filen *Pom. XML* i en text redigerare och Lägg till en `<dependency>` for. `spring-cloud-azure-appconfiguration-config-web`

**Våren Cloud 1.1. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

**Våren Cloud 1.2. x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

Spara filen och skapa och kör sedan programmet som vanligt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat din våren Boot-app för att dynamiskt uppdatera konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
