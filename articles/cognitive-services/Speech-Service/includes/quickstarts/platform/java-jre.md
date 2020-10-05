---
title: 'Snabb start: tal-SDK för Java (Windows, Linux, macOS) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för att använda Java (Windows, Linux, macOS) med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 2a6516ad5f0fc8d9faefd7b7f89ddb1eaa3fd7d6
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375268"
---
Den här guiden visar hur du installerar [talet SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för 64-bitars Java 8-JRE. Om du bara vill att paket namnet ska komma igång på egen hand är Java SDK inte tillgängligt i maven Central-lagringsplatsen. Oavsett om du använder Gradle eller en `pom.xml` beroende fil måste du lägga till en anpassad databas som pekar på `https://csspeechstorage.blob.core.windows.net/maven/` (se nedan för paketets namn).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- Java Speech SDK-paketet är tillgängligt för dessa operativ system:
  - Windows: 64-endast bit
  - Mac: macOS X version 10,13 eller senare
  - Linux: 64-endast bit på Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 7/8, CentOS 7/8

## <a name="prerequisites"></a>Förutsättningar

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Sol förmörkelse Java IDE](https://www.eclipse.org/downloads/) (kräver Java redan installerat)
- Linux-plattformar som stöds kräver vissa bibliotek installerade ( `libssl` för Secure Sockets Layer-stöd och `libasound2` för ljud support). Se distributionen nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

  - På Ubuntu kör du följande kommandon för att installera de nödvändiga paketen:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  - På Debian 9 kör du följande kommandon för att installera de nödvändiga paketen:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

  - På RHEL/CentOS kör du följande kommandon för att installera de nödvändiga paketen:

    ```sh
    sudo yum update
    sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
    ```

> [!NOTE]
> - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Observera att installationen för första gången kan kräva att du startar om Windows innan du fortsätter med den här guiden.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Skapa ett Sol förmörkelse-projekt och installera talet SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
