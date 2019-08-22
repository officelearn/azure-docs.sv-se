---
title: Säkerhets rekommendationer för Azure IoT | Microsoft Docs
description: Den här artikeln sammanfattar ytterligare steg för att säkerställa säkerheten i din Azure IoT Hub-lösning.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 55ca189d5f4622a395ffe603d7f0d6764db82f3d
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877225"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Säkerhets rekommendationer för Azure Sakernas Internet-distribution (IoT)

Den här artikeln innehåller säkerhets rekommendationer för Azure IoT-tjänster. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter som en kund av Azure IoT och förbättra den övergripande säkerheten för dina IoT-lösningar. För mer information om de inbyggda säkerhetsfunktionerna i Azure IoT, Läs [IoT-säkerhet från grunden](iot-security-ground-up.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentar |
|-|-|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar som stöds, programmeringsspråk, protokoll och ramverk. |
| Skydda nycklar säkert | Behåll enhets-ID: n och deras autentiseringsinställningar fysiskt säkra efter distributionen. Detta undviker en skadlig enhet som är maskerad som en registrerad enhet. |
| Använd enhets-SDK: er om möjligt | Enhets-SDK: er implementerar olika säkerhetsfunktioner, till exempel kryptering, autentisering och så vidare, för att hjälpa dig att utveckla ett robust och säkert enhets program. Mer information finns i [förstå och använda Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) : er. |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentar |
|-|-|
| Definiera åtkomst kontroll för hubben | [Förstå och definiera vilken typ av åtkomst](iot-security-deployment.md#securing-the-cloud) varje komponent kommer att ha i din IoT Hub-lösning, baserat på funktionerna. De tillåtna behörigheterna är *Registry Read*, *RegistryReadWrite*, *ServiceConnect*och *DeviceConnect*. Standard [principer för delad åtkomst i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan också hjälpa dig att definiera behörigheter för varje komponent baserat på dess roll. |
| Definiera åtkomst kontroll för backend-tjänster | Data som matas in av IoT Hub-lösningen kan användas av andra Azure-tjänster som [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)och [blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Se till att förstå och bevilja lämpliga åtkomst behörigheter som dokumenterade för de här tjänsterna. |


## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentar |
|-|-|
| Säker enhetsautentisering | Säkerställ säker kommunikation mellan dina enheter och IoT Hub genom att antingen använda [en unik identitets nyckel eller säkerhetstoken](iot-security-deployment.md#iot-hub-security-tokens), eller [ett 509-certifikat](iot-security-deployment.md#x509-certificate-based-device-authentication) för varje enhet. Använd lämplig metod för att [använda säkerhetstoken baserat på det valda protokollet (MQTT, AMQP eller https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Säker enhets kommunikation | IoT Hub skyddar anslutningen till enheterna med hjälp av Transport Layer Security (TLS) standard, stödda versioner 1,2 och 1,0. Använd [TLS 1,2](https://tools.ietf.org/html/rfc5246) för att säkerställa maximal säkerhet. |
| Säker tjänst kommunikation | IoT Hub tillhandahåller slut punkter för att ansluta till backend-tjänster som [Azure Storage](/azure/storage/) eller [Event HUBS](/azure/event-hubs) endast med TLS-protokollet och ingen slut punkt exponeras på en okrypterad kanal. När dessa data når dessa backend-tjänster för lagring eller analys, se till att du använder lämpliga säkerhets-och krypterings metoder för tjänsten och skyddar känslig information på Server delen. |


## <a name="networking"></a>Nätverk

| Rekommendation | Kommentar |
|-|-|
| Skydda åtkomsten till dina enheter | Se till att maskin varu portarna i enheterna är minimala för att undvika oönskad åtkomst. Dessutom kan du bygga mekanismer för att förhindra eller identifiera fysisk manipulering av enheten. Läs [metod tips för IoT-säkerhet](iot-security-best-practices.md) för mer information. |
| Bygg säker maskin vara | Använd säkerhetsfunktioner som krypterad lagring eller Trusted Platform Module (TPM) för att hålla enheterna och infrastrukturen säkrare. Se till att enhetens operativ system och driv rutiner har uppgraderats till de senaste versionerna, och om det är möjligt att installera antivirus program och program mot skadlig kod. Läs [IoT](iot-security-architecture.md) -säkerhetsarkitekturen för att förstå hur det kan hjälpa till att undvika flera säkerhetshot. |


## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentar |
|-|-|
| Övervaka obehörig åtkomst till dina enheter |  Använd enhetens operativ Systems loggnings funktion för att övervaka säkerhets överträdelser eller fysisk manipulering av enheten eller dess portar. |
| Övervaka din IoT-lösning från molnet | Övervaka den övergripande hälsan för din IoT Hub lösning med hjälp av [måtten i Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Konfigurera diagnostik | Titta närmare på dina åtgärder genom att logga händelser i din lösning och skicka sedan diagnostikloggar till Azure Monitor för att få insyn i prestandan. Läs [övervaka och diagnostisera problem i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) för mer information. |

## <a name="next-steps"></a>Nästa steg

För avancerade scenarier som involverar Azure IoT kan du behöva överväga ytterligare säkerhets krav. Mer information finns i [säkerhets arkitekturen för IoT](iot-security-architecture.md) .

