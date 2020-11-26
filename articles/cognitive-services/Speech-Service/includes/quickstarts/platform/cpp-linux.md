---
title: 'Snabb start: tal SDK C++ (Linux) plattforms konfiguration – tal tjänst'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att konfigurera din plattform för C++ på Linux med Speech service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 10cee7f5b0deff37a9b1df1937fe8f6ed8431daa
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188380"
---
Den här guiden visar hur du installerar [talet SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) för Linux

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Systemkrav

Linux Se listan över [Linux-distributioner och mål arkitekturer som stöds](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten behöver du:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Linux-plattformar som stöds kräver vissa bibliotek installerade ( `libssl` för Secure Sockets Layer-stöd och `libasound2` för ljud support). Se distributionen nedan för de kommandon som behövs för att installera rätt versioner av dessa bibliotek.

   * På Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Om libssl 1.0.0 inte är tillgängligt installerar du libssl 1.0. x (där x är större än 0) eller libssl 1.1 i stället.

   * På RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - På RHEL/CentOS 7 följer du anvisningarna för [hur du konfigurerar RHEL/CentOS 7 för tal-SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - På RHEL/CentOS 8 följer du anvisningarna för [hur du konfigurerar openssl för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [windows](../quickstart-list.md)]
