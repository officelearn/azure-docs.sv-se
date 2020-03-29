---
title: Ansluta till Azure Media Services v3 API - Java
description: I den här artikeln beskrivs hur du ansluter till Azure Media Services v3 API med Java.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888503"
---
# <a name="connect-to-media-services-v3-api---java"></a>Ansluta till Media Services v3 API - Java

Den här artikeln visar hur du ansluter till Azure Media Services v3 Java SDK med hjälp av tjänstens huvudloggmetod.

I den här artikeln används Visual Studio-koden för att utveckla exempelappen.

## <a name="prerequisites"></a>Krav

- Följ [Skriva Java med Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) för att installera:

   - JDK
   - Apache Maven
   - Java-tilläggspaket
- Se till `JAVA_HOME` att `PATH` ställa in och miljövariabler.
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Kom ihåg resursgruppsnamnet och mediatjänstkontonamnet.
- Följ stegen i avsnittet [Access API:er.](access-api-cli-how-to.md) Registrera prenumerations-ID, program-ID (klient-ID), autentiseringsnyckeln (hemlig) och klient-ID som du behöver i ett senare steg.

Också granska:

- [Java i Visual Studio-kod](https://code.visualstudio.com/docs/languages/java)
- [Java Projektledning i VS-kod](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Granska [namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Skapa ett Maven-projekt

Öppna ett kommandoradsverktyg `cd` och till en katalog där du vill skapa projektet.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

När du kör kommandot `pom.xml` `App.java`skapas , och andra filer. 

## <a name="add-dependencies"></a>Lägga till beroenden

1. Öppna mappen där projektet är i Visual Studio-kod
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

## <a name="connect-to-the-java-client"></a>Anslut till Java-klienten

1. Öppna `App.java` filen `src\main\java\com\azure\ams` under och se till att ditt paket ingår högst upp:

    ```java
    package com.azure.ams;
    ```
1. Lägg till dessa importsatser under paketutdraget:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Om du vill skapa de Active Directory-autentiseringsuppgifter som du behöver göra begäranden lägger du till följande kod i huvudmetoden för klassen App och anger de värden som du fick från [Access-API:er:](access-api-cli-how-to.md)
   
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

## <a name="see-also"></a>Se även

- [Koncept för Media Services](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referens](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Nästa steg

Du kan `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` nu inkludera och börja manipulera entiteter.

Fler kodexempel finns i [Java SDK-exemplen](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) repo.
