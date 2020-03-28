---
title: 'Snabbstart: Tal SDK för Java (Windows, Linux, macOS) plattform setup - Tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för att använda Java (Windows, Linux, macOS) med taltjänsten SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925460"
---
Den här guiden visar hur du installerar [Tal SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för 64-bitars Java 8 JRE. Om du bara vill att paketnamnet ska komma igång på egen hand är Java SDK inte tillgängligt i Mavens centrala databas. Oavsett om du använder `pom.xml` Gradle eller en beroendefil måste du `https://csspeechstorage.blob.core.windows.net/maven/` lägga till en anpassad databas som pekar på (se nedan för paketnamn).

> [!NOTE]
> Information om Speech Devices SDK och Roobo-enheten finns i [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Operativsystem som stöds

- Java Speech SDK-paketet är tillgängligt för dessa operativsystem:
  - Windows: endast 64 bitar
  - Mac: macOS X version 10.13 eller senare
  - Linux: 64-bitars endast på Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Krav

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) eller [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (kräver Java redan installerat)
- Linux-plattformar som stöds kräver`libssl` vissa installerade bibliotek (för stöd för säkert socketlager och `libasound2` för ljudstöd). Se din distribution nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

  - På Ubuntu kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - På Debian 9 kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - På RHEL/CentOS 8 kör du följande kommandon för att installera de paket som krävs:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> På RHEL/CentOS 8 följer du instruktionerna för [hur du konfigurerar OpenSSL för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- I Windows behöver du [Microsoft Visual C++ Redistributable för Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) för din plattform. Observera att om du installerar detta för första gången kan det krävas att du startar om Windows innan du fortsätter med den här guiden.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Skapa ett Eclipse-projekt och installera Tal-SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
