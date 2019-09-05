---
title: Ansluta till Azure Media Services v3 API-Java
description: Lär dig hur du ansluter till Media Services v3 API med Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: f89e5cc434403e4edc3501d24ce2e94664d13ae9
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307850"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ansluta till Media Services v3 API-Java

Den här artikeln visar hur du ansluter till Azure Media Services v3 Java SDK med hjälp av inloggnings metoden för tjänstens huvud namn.

I den här artikeln används Visual Studio Code för att utveckla exempel appen.

## <a name="prerequisites"></a>Förutsättningar

- Följ [Skriv Java med Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) för att installera:

   - JDK
   - Apache Maven
   - Java Extension Pack
- Se till att ställa `JAVA_HOME` in `PATH` och miljövariabler.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Glöm inte att komma ihåg resurs gruppens namn och namnet på Media Services kontot.
- Följ stegen i avsnittet [åtkomst-API: er](access-api-cli-how-to.md) . Registrera prenumerations-ID, program-ID (klient-ID), den autentiseringsnyckel (hemlighet) och klient-ID som du behöver i ett senare steg.

Granska även:

- [Java i Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java projekt hantering i VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Granska [namngivnings konventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Öppna ett kommando rads verktyg och `cd` till en katalog där du vill skapa projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

När du kör kommandot `pom.xml`skapas, `App.java`, och andra filer. 

## <a name="add-dependencies"></a>Lägg till beroenden

1. Öppna mappen där projektet finns i Visual Studio Code
1. Hitta och öppna`pom.xml`
1. Lägg till nödvändiga beroenden

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Ansluta till Java-klienten

1. Öppna filen under `src\main\java\com\azure\ams` och kontrol lera att paketet ingår längst upp: `App.java`

    ```java
    package com.azure.ams;
    ```
1. Lägg till följande import uttryck under paket instruktionen:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Om du vill skapa de Active Directory autentiseringsuppgifter som du behöver för att göra förfrågningar lägger du till följande kod till main-metoden i klassen app och anger de värden som du har fått från [åtkomst till API: er](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Kör appen.

## <a name="see-also"></a>Se också

- [Media Services begrepp](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referens](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Nästa steg

Nu kan du ta `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` med och börja ändra entiteter.
