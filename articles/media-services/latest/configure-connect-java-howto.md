---
title: Ansluta till Azure Media Services v3-API – Java
description: Lär dig hur du ansluter till Media Services v3-API med Java.
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
ms.date: 04/16/2019
ms.author: juliako
ms.openlocfilehash: 27a4a30dd0eb449726a99d02f2409632aa327567
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683434"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ansluta till Media Services v3-API – Java

Den här artikeln visar hur du ansluter till Azure Media Services v3 Java SDK med det tjänstens huvudnamn i metoden.

I den här artikeln används Visual Studio Code för att utveckla exempelappen.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Följ [skriva Java med Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) att installera:

   - JDK
   - Apache Maven
   - Java-tilläggspaketet
- Se till att ange `JAVA_HOME` och `PATH` miljövariabler.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Var noga med att komma ihåg resursgruppens namn och namnet på Media Services-konto.
- Följ stegen i den [åtkomst API: er](access-api-cli-how-to.md) avsnittet. Anteckna prenumerations-ID, program-ID (klient-ID), autentiseringsnyckeln (hemligt) och klient-ID som du behöver i ett senare steg.

Granska även:

- [Java i Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Hantering av Java-projektet i VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Öppna Kommandotolken och `cd` till den katalog där du vill skapa projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

När du kör kommandot, den `pom.xml`, `App.java`, och andra filer skapas. 

## <a name="add-dependencies"></a>Lägga till beroenden

1. Öppna mappen där det är ditt projekt i Visual Studio Code
1. Leta upp och öppna den `pom.xml`
1. Lägg till nödvändiga beroenden

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Anslut till Java-klient

1. Öppna den `App.java` filen under `src\main\java\com\azure\ams` och se till att ditt paket ingår överst:

    ```java
    package com.azure.ams;
    ```
1. Under instruktionen paketet lägger du till dessa import-satserna:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Lägg till följande kod i main-metoden i klassen App för att skapa Active Directory-autentiseringsuppgifter som du behöver göra begäranden, och ange värden som du fick från [åtkomst API: er](access-api-cli-how-to.md):
   
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

- [Media Services-koncepten](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referens](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Nästa steg

Du kan nu inkludera `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` och börja hantera enheter.
