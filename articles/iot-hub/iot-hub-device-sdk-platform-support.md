---
title: Azure IoT-enhet SDKs-plattformssupport | Microsoft-dokument
description: SDK:er med öppen källkod är tillgängliga på GitHub i C, .NET (C#), Java, Node.js och Python för att ansluta enheter till Azure IoT Hub and Device Provisioning Service (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045130"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Azure IoT-enhet SDKs-plattformsstöd

Microsoft strävar efter att kontinuerligt utöka universum av Azure IoT Hub-kompatibla enheter. Microsoft publicerar SDK:er med öppen källkod på GitHub för att ansluta enheter till Azure IoT Hub och enhetsetableringstjänsten. EnhetenSDK:er är tillgängliga för C, .NET (C#), Java, Node.js och Python. Microsoft testar varje SDK för att säkerställa att den körs på de konfigurationer som stöds som beskrivs för den i supportavsnittet för [Microsoft SDK och enhetsplattform.](#microsoft-sdks-and-device-platform-support)

Förutom enhetens SDK:er tillhandahåller Microsoft flera andra vägar för att ge kunder och utvecklare möjlighet att ansluta sina enheter till Azure IoT:

* Microsoft samarbetar med flera partnerföretag för att hjälpa dem att publicera utvecklingspaket, baserade på Azure IoT C SDK, för deras maskinvaruplattformar.

* Microsoft samarbetar med Microsofts betrodda partner för att tillhandahålla en ständigt växande uppsättning enheter som har testats och certifierats för Azure IoT. En aktuell lista över dessa enheter finns i [Azure-certifierad för IoT-enhetskatalog](https://catalog.azureiotsolutions.com/).

* Microsoft tillhandahåller ett PAL (Platform Abstraction Layer) i Azure IoT Hub Device C SDK som hjälper utvecklare att enkelt portera SDK till sin plattform. Mer information finns i [C SDK-porteringsvägledningen](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

Det här avsnittet innehåller information om Microsoft SDK:er och de plattformskonfigurationer som de stöder, samt vart och ett av de andra alternativen som anges ovan.

## <a name="microsoft-sdks-and-device-platform-support"></a>Stöd för Microsoft SDK:er och enhetsplattform

Microsoft publicerar SDK:er med öppen källkod på GitHub för följande språk: C, .NET (C#), Node.js, Java och Python. SDK:erna och deras beroenden visas i det här avsnittet. SDK:erna stöds på alla enhetsplattformar som uppfyller dessa beroenden.

For each of the listed SDKs, Microsoft:

* Bygger och kör kontinuerligt end-to-end-tester mot huvudgrenen för relevant SDK i GitHub på flera populära plattformar.  För att ge testtäckning över olika kompilatorversioner testar vi i allmänhet mot den senaste LTS-versionen och den mest populära versionen.

* Ger installationsvägledning eller installationspaket om tillämpligt.

* Stöder SDK:erna på GitHub med öppen källkod, en sökväg för kundbidrag och produktteamsengagemang med GitHub-problem.

### <a name="c-sdk"></a>C SDK

[Azure IoT Hub C-enheten SDK](https://github.com/Azure/azure-iot-sdk-c) testas med och stöder följande konfigurationer.

| Operativsystem                  | TLS-bibliotek                  | Ytterligare krav                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL eller BearSSL | Berkeley uttag</br></br>Det portabla operativsystemgränssnittet (POSIX)                       |
| iOS 12.2            | OpenSSL                      | XCode emukulerat i OSX 10.13.4                                                               |
| Windows 10-familjen   | Schannel                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT dev kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sfär OS     | Vargssl                      | [Azure Sfär MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | Björnssl                      | [ESP32 eller ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[Azure IoT Hub Python-enheten SDK](https://github.com/Azure/azure-iot-sdk-python) testas med och stöder följande konfigurationer.

| Operativsystem                  | Kompilator                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7.*, 3.5 eller senare |
| MacOS höga Sierra   | Python 2.7.*, 3.5 eller senare |
| Windows 10-familjen   | Python 2.7.*, 3.5 eller senare |

Endast Python version 3.5.3 eller senare stöder asynkrona API:er rekommenderar vi att du använder version 3.7 eller senare.

### <a name="net-sdk"></a>.NET SDK

[Azure IoT Hub .NET (C#) enhet SDK](https://github.com/Azure/azure-iot-sdk-csharp) testas med och stöder följande konfigurationer.

| Operativsystem                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Stationära och serverscku:er   | .NET Core 2.1, .NET Framework 4.5.1 eller .NET Framework 4.7 |

.NET SDK kan också användas med Windows IoT Core med [Azure Device Agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) eller en anpassad [NTService som kan använda RPC för att kommunicera med UWP-program](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

[Azure IoT Hub Node.js-enheten SDK](https://github.com/Azure/azure-iot-sdk-node) testas med och stöder följande konfigurationer.

| Operativsystem                  | Nodversion    |
|---------------------|-----------------|
| Linux               | LTS och aktuell |
| Windows 10-familjen   | LTS och aktuell |

### <a name="java-sdk"></a>Java SDK

[Azure IoT Hub Java-enheten SDK](https://github.com/Azure/azure-iot-sdk-java) testas med och stöder följande konfigurationer.

| Operativsystem                     | Java-version |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10-familjen x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Partnerstödda utvecklingspaket

Microsoft samarbetar med olika partner för att tillhandahålla utvecklingspaket för flera mikroprocessorarkitekturer. Dessa partner har portat Azure IoT C SDK till sin plattform. Partners skapar och underhåller plattformsabstraktionslagret (PAL) för SDK. Microsoft samarbetar med dessa partner för att ge utökad support.

| Partner             | Enheter                            | Länk                     | Support |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif (Espressif)           | ESP32 <br/> ESP8266                              | [Esp-azurblå](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE för IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Mikroelektronik | STM32L4-serien <br/> STM32F4-serien <br/>  STM32F7-serien <br/>  STM32L4 Discovery Kit för IoT-nod    | [X-KUB-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Support](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instrument   | CC3220SF LaunchPad </br> CC3220S LaunchPad </br> CC3235SF LaunchPad </br> CC3235S LaunchPad </br> MSP432E4 LaunchPad | [Azure IoT-plugin för SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E Forum](https://e2e.ti.com) <br/> [TI E2E Forum för CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E Forum för MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Portera Microsoft Azure IoT C SDK

Om din enhetsplattform inte omfattas av något av föregående avsnitt kan du överväga att portera Azure IoT C SDK. Portering av C SDK innebär i första hand att implementera plattformsabstraktionslagret (PAL) för SDK. PAL definierar primitiver som ger limmet mellan enheten och funktioner på högre nivå i SDK. Mer information finns i [Portningsvägledning](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Microsoft-partner och certifierade Azure IoT-enheter

Microsoft samarbetar med ett antal partner för att kontinuerligt utöka Azure IoT-universumet med Azure IoT-testade och certifierade enheter.

* Information om hur du bläddrar bland Azure IoT-certifierade enheter finns i [Microsoft Azure Certified for IoT Device Catalog](https://catalog.azureiotsolutions.com/).

* Mer information om Azure Certified för IoT-ekosystem finns i [Gå med i ekosystemet Certified for IoT](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Ansluta till IoT Hub utan SDK

Om du inte kan använda någon av IoT Hub-enhetens SDK:er kan du ansluta direkt till IoT Hub med hjälp av [IoT Hub REST API:er](https://docs.microsoft.com/rest/api/iothub/) från alla program som kan skicka och ta emot HTTPS-begäranden och svar.

## <a name="support-and-other-resources"></a>Stöd och andra resurser

Om du får problem när du använder Azure IoT-enhetenSDK:er finns det flera sätt att söka support. Du kan prova någon av följande kanaler:

**Rapportera fel** – Buggar i enhetenSDK kan rapporteras på ärendesidan i det relevanta GitHub-projektet. Korrigeringar tar sig snabbt från projektet till produktuppdateringar.

* [Azure IoT Hub C SDK-problem](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Azure IoT Hub .NET (C#) SDK-problem](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Azure IoT Hub Java SDK-problem](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Azure IoT Hub Node.js SDK-problem](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Azure IoT Hub Python SDK-problem](https://github.com/Azure/azure-iot-sdk-python/issues)

**Microsoft Customer Support team** – Användare som har en [supportplan](https://azure.microsoft.com/support/plans/) kan engagera Microsofts kundtjänst genom att skapa en ny supportbegäran direkt från [Azure-portalen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

**Funktionsförfrågningar** – Azure IoT-funktionsbegäranden spåras via produktens [användarröstsida](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="next-steps"></a>Nästa steg

* [SDK:er för tjänst och enhet](iot-hub-devguide-sdks.md)
* [Portning Vägledning](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
