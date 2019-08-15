---
title: 'Plattforms stöd för Azure IoT Device SDK: er | Microsoft Docs'
description: 'Begrepp – lista över plattformar som stöds av SDK: er för Azure IoT-enheter'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: robinsh
ms.openlocfilehash: 1fbaa54be4419d493d5179a6d6d0884393c8138a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967329"
---
# <a name="azure-iot-sdks-platform-support"></a>Plattforms stöd för Azure IoT SDK

[Azure IoT SDK](iot-hub-devguide-sdks.md) : er är en uppsättning bibliotek som används för att interagera med IoT Hub och enhets etablerings tjänsten med bred språk-och plattforms stöd. SDK: erna körs på de vanligaste plattformarna och utvecklare kan portarna C SDK till en specifik plattform genom att följa [rikt linjerna för porten](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Microsoft stöder flera olika operativ system/plattformar/ramverk och kan utökas med hjälp av Azure IoT C SDK. Vissa stöds officiellt av teamet, grupperade i nivåer som representerar den nivå av support användare som kan förväntas. *Plattformar som stöds fullt ut* innebär att Microsoft:

- Skapar och kör kontinuerligt tester från slut punkt till slut punkt mot Master-och LTS-versioner som stöds.  För att tillhandahålla test täckning för olika versioner testar vi vanligt vis mot den senaste LTS-versionen och den mest populära versionen.  Andra versioner av samma plattform kan stödjas via plattforms versionens kompatibilitet.
- Tillhandahåller installations vägledning eller paket om tillämpligt.
- Har fullt stöd för plattformarna på GitHub.

Dessutom har en lista över partners en port för vår C SDK på fler plattformar och de upprätthåller plattforms abstraktions skiktet (PAL). [Azure-certifierad för IoT-katalogen](https://catalog.azureiotsolutions.com/) innehåller också en lista över OS-plattformar som de olika SDK: erna har testats mot. SDK: er bygger också regelbundet på dessa plattformar med begränsad testning och support:

* MBED2
* Arduino
* Windows CE 2013 (inaktuell i oktober 2018)
* .NET standard 1,3 med .NET Core 2,1 och .NET Framework 4,7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Plattformar som stöds

Det finns flera plattformar som stöds.

### <a name="c-sdk"></a>C SDK

| OS                  | Båge | Kompilatorn             | TLS-bibliotek       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc – 5.4.0            | openssl – 1.0.2 g |
| Ubuntu 18,04 LTS    | X64  | gcc – 7,3              | WolfSSL – 1,13    |
| Ubuntu 18,04 LTS    | X64  | Clang 6.0. X          | Openssl – 1.1.0 g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Ursprunglig OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9-sträckning    | x64  | gcc – 7,3              | Openssl – 1.1.0 f  |

### <a name="python-sdk"></a>Python SDK

| OS                  | Båge | Kompilatorn   | TLS-bibliotek |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18,04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18,04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS, hög Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK

| OS                  | Båge | Samband            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET standard 2,0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET standard 2,0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | Gäller inte               |

### <a name="nodejs-sdk"></a>Node.js SDK

| OS                                           | Båge | Node-version |
|----------------------------------------------|------|--------------|
| Ubuntu 16,04-LTS (med nod 6 Docker-avbildning) | X64  | Node 6       |
| Windows Server 2016                          | X64  | Node 6       |

### <a name="java-sdk"></a>Java SDK

| OS                  | Båge | Java-version |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Android Things | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Plattformar som stöds av partner

Kunder kan utöka vår plattforms support genom att Porta Azure IoT C SDK, särskilt skapa plattforms abstraktions skiktet (PAL) för SDK: n. Microsoft samarbetar med partner för att ge utökad support. En lista över partners har hamnat på C SDK på fler plattformar och har en bättre hantering av PAL.

| Partner             | Enheter                            | Länka                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE för IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| S:T mikroelektroniska | STM32L4-serien <br/> STM32F4-serien <br/>  STM32F7-serien <br/>  STM32L4 Discovery Kit for IoT-nod    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Support](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Start fönstret </br> CC3220S Start fönstret </br> CC3235SF Start fönstret </br> CC3235S Start fönstret </br> MSP432E4 Start fönstret | [Azure IoT-plugin-program för SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E-forum](https://e2e.ti.com) <br/> [TI E2E-forumet för CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E-forumet för MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Nästa steg

* [SDK:er för tjänst och enhet](iot-hub-devguide-sdks.md)
* [Vägledning för porting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
