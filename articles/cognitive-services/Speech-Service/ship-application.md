---
title: Utveckla appar med tjänsten Tal-SDK - Tal
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar ett program som använder Tal-SDK på plattformar som stöds.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: 9507428e63b337b3d8419a833d03d081d494c522
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330812"
---
# <a name="ship-an-application"></a>Skicka ett program

Observera [Speech SDK-licensen](https://aka.ms/csspeech/license201809)samt meddelanden från programvara från tredje part när du [distribuerar](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html) Azure Cognitive Services Speech SDK. Läs även [Microsofts sekretesspolicy](https://aka.ms/csspeech/privacy).

Beroende på plattformen finns det olika beroenden för att köra ditt program.

## <a name="windows"></a>Windows

Cognitive Services Speech SDK testas på Windows 10 och Windows Server 2016.

Cognitive Services Speech SDK kräver [Microsoft Visual C++ Redist som kan tillskrivas Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) på systemet. Du kan ladda ner installatörer `Microsoft Visual C++ Redistributable for Visual Studio 2019` för den senaste versionen av här:

- [Win32 (på andra sätt)](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

Om programmet använder hanterad `.NET Framework 4.6.1` kod krävs eller senare på måldatorn.

För mikrofoninmatning måste Media Foundations bibliotek installeras. Dessa bibliotek är en del av Windows 10 och Windows Server 2016. Det är möjligt att använda Tal SDK utan dessa bibliotek, så länge en mikrofon inte används som ljudingångsenhet.

De tal-SDK-filer som krävs kan distribueras i samma katalog som ditt program. På så sätt kan ditt program komma åt biblioteken direkt. Se till att du väljer rätt version (Win32/x64) som matchar ditt program.

| Namn | Funktion |
| :--- | :------- |
| `Microsoft.CognitiveServices.Speech.core.dll`   | Core SDK, som krävs för inbyggd och hanterad distribution |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | Krävs för hanterad distribution                      |

> [!NOTE]
> Från och med utgivningen 1.3.0 behövs inte filen `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (levererad i tidigare versioner) längre. Funktionaliteten är nu integrerad i kärnan SDK.

> [!NOTE]
> För Projektet Windows Forms App (.NET Framework) C# kontrollerar du att biblioteken ingår i projektets distributionsinställningar. Du kan kontrollera `Properties -> Publish Section`detta under . Klicka `Application Files` på knappen och hitta motsvarande bibliotek från rullningslistan. Kontrollera att värdet är `Included`inställt på . Visual Studio kommer att innehålla filen när projektet publiceras/distribueras.

## <a name="linux"></a>Linux

Speech SDK stöder för närvarande Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8 distributioner.
För ett inbyggt program måste du skicka `libMicrosoft.CognitiveServices.Speech.core.so`Speech SDK-biblioteket.
Se till att du väljer den version (x86, x64) som matchar ditt program. Beroende på Linux-versionen kan du också behöva inkludera följande beroenden:

- De delade biblioteken i GNU C-biblioteket (inklusive POSIX-trådar programmeringsbiblioteket, `libpthreads`)
- OpenSSL-biblioteket`libssl.so.1.0.0` ( `libssl.so.1.0.2`eller )
- Det delade biblioteket för ALSA-applikationer (`libasound.so.2`)

På Ubuntu bör GNU C-biblioteken redan installeras som standard. De tre sista kan installeras med hjälp av följande kommandon:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

På Debian 9 installera dessa paket:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

På RHEL/CentOS 8:

```sh
sudo yum update
sudo yum install alsa-lib openssl
```

> [!NOTE]
> På RHEL/CentOS 8 följer du instruktionerna för [hur du konfigurerar OpenSSL för Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Se hur du känner igen tal i C #](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
