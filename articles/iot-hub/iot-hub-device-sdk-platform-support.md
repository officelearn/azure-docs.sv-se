---
title: 'Azure IoT SDK: er stöd för Enhetsplattform | Microsoft Docs'
description: Begrepp - lista över plattformar som stöds av Azure IoT Device SDK
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043991"
---
# <a name="azure-iot-sdks-platform-support"></a>Plattformsstödet för Azure IoT SDK: er

[Azure IoT SDK: er](iot-hub-devguide-sdks.md) är en uppsättning bibliotek att interagera med IoT Hub och Device Provisioning-tjänsten med bred språk och stöd för plattformar.  SDK: erna som körs på de vanligaste plattformarna och utvecklare kan port C SDK, för att viss plattform genom att följa den [porta vägledning](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Microsoft stöder en mängd olika operativsystem/plattformar/ramverk och kan utökas med hjälp av Azure IoT C SDK.  Vissa stöds officiellt av teamet grupperade i nivåer som representerar supportnivå de kan förvänta sig.  Fullständigt stöd plattformar innebär att Microsoft:
    - Kontinuerligt skapa och test för slutpunkt till slutpunkt mot master och LTS versioner som stöds
    - Ange installationsguide eller paket om så är tillämpligt
    - Fullständigt stöd på GitHub

Dessutom kan en lista med partner har porteras vår C SDK in fler plattformar och de underhålla plattform abstraction layer (PAL).  [Azure Certified for IoT-Enhetskatalog](https://catalog.azureiotsolutions.com/) också funktioner en lista över de olika SDK: er för OS-plattformar har testats mot.  Dessutom SDK: erna även regelbundet skapa på de här plattformarna med begränsad testning och stöder:
- MBED2
- Arduino
- Windows CE 2013 (inaktualisera i oktober 2018)
- .NET standard 1.3 med .NET Core 2.1 och .NET Framework 4.7
- Xamarin iOS, Android, UWP
- Android med Java

## <a name="supported-platforms"></a>Plattformar som stöds
### <a name="c-sdk"></a>C SDK
| Operativsystem                  | Arch | Kompilatorn             | TLS-biblioteket       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Interna OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK
| Operativsystem                  | Arch | Kompilatorn   | TLS-biblioteket |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK
| Operativsystem                  | Arch | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET framework 4.7   | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET framework 4.5.1 | Gäller inte               |

### <a name="nodejs-sdk"></a>Node.js SDK
| Operativsystem                                           | Arch | Nodversionen |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (med noden 6 docker-avbildning) | X64  | Noden 6       |
| Windows Server 2016                          | X64  | Noden 6       |

### <a name="java-sdk"></a>Java SDK
| Operativsystem                  | Arch | Java-version |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>Plattformar som stöds av partner
| Partner             | Enheter                            | Länk                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE för IoT-SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 serien STM32F4-serien      | [X-KUB-MOLN](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [Support](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | STM32F7-serien                     | [X-KUBEN AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | STM32L4 identifiering Kit för IoT-nod | [AZURE-P-NUCLEO](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [FP-CLD AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [ESP-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>Nästa steg
- [SDK:er för tjänst och enhet](iot-hub-devguide-sdks.md)
- [Porteringsguiden för](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)