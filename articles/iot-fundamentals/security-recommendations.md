---
title: Säkerhets rekommendationer för Azure IoT | Microsoft Docs
description: Den här artikeln sammanfattar ytterligare steg för att säkerställa säkerheten i din Azure IoT Hub-lösning.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 0ada9a520a5be56444a1c3e746a68dbcf9275686
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048463"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Säkerhets rekommendationer för Azure Sakernas Internet-distribution (IoT)

Den här artikeln innehåller säkerhets rekommendationer för IoT. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhets skyldigheter enligt beskrivningen i vår delade ansvars modell. Mer information om vad Microsoft gör för att uppfylla ansvaret för service leverantörer finns i [delade ansvars områden för molnbaserad data behandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Några av rekommendationerna som ingår i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvars linjen i att skydda dina resurser i Azure. Den analyserar regelbundet säkerhets läget för dina Azure-resurser för att identifiera potentiella säkerhets risker. Sedan får du rekommendationer om hur du kan åtgärda dem.

- Mer information om Azure Security Center rekommendationer finns [i säkerhets rekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).
- Mer information om Azure Security Center finns i [Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar som stöds, programmeringsspråk, protokoll och ramverk. | - |
| Skydda nycklar säkert | Behåll enhets-ID: n och deras autentiseringsinställningar fysiskt säkra efter distributionen. Detta undviker en skadlig enhet som är maskerad som en registrerad enhet. | - |
| Använd enhets-SDK: er om möjligt | Enhets-SDK: er implementerar olika säkerhetsfunktioner, till exempel kryptering, autentisering och så vidare, för att hjälpa dig att utveckla ett robust och säkert enhets program. Mer information finns i [förstå och använda Azure IoT Hub SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) : er. | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering 

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Definiera åtkomst kontroll för hubben | [Förstå och definiera vilken typ av åtkomst](iot-security-deployment.md#securing-the-cloud) varje komponent kommer att ha i din IoT Hub-lösning, baserat på funktionerna. De tillåtna behörigheterna är *Registry Read*, *RegistryReadWrite*, *ServiceConnect*och *DeviceConnect*. Standard [principer för delad åtkomst i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan också hjälpa dig att definiera behörigheter för varje komponent baserat på dess roll. | - |
| Definiera åtkomst kontroll för backend-tjänster | Data som matas in av IoT Hub-lösningen kan användas av andra Azure-tjänster som [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service](https://docs.microsoft.com/azure/app-service/), [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)och [blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Se till att förstå och bevilja lämpliga åtkomst behörigheter som dokumenterade för de här tjänsterna. | - |

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Säker enhetsautentisering | Säkerställ säker kommunikation mellan dina enheter och IoT Hub genom att antingen använda [en unik identitets nyckel eller säkerhetstoken](iot-security-deployment.md#iot-hub-security-tokens), eller [ett 509-certifikat](iot-security-deployment.md#x509-certificate-based-device-authentication) för varje enhet. Använd lämplig metod för att [använda säkerhetstoken baserat på det valda protokollet (MQTT, AMQP eller https)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). | - |
| Säker enhets kommunikation | IoT Hub skyddar anslutningen till enheterna med hjälp av Transport Layer Security (TLS) standard, stödda versioner 1,2 och 1,0. Använd [TLS 1,2](https://tools.ietf.org/html/rfc5246) för att säkerställa maximal säkerhet. | - |
| Säker tjänst kommunikation | IoT Hub tillhandahåller slut punkter för att ansluta till backend-tjänster som [Azure Storage](/azure/storage/) eller [Event HUBS](/azure/event-hubs) endast med TLS-protokollet och ingen slut punkt exponeras på en okrypterad kanal. När dessa data når dessa backend-tjänster för lagring eller analys, se till att du använder lämpliga säkerhets-och krypterings metoder för tjänsten och skyddar känslig information på Server delen. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Skydda åtkomsten till dina enheter | Se till att maskin varu portarna i enheterna är minimala för att undvika oönskad åtkomst. Dessutom kan du bygga mekanismer för att förhindra eller identifiera fysisk manipulering av enheten. Läs [metod tips för IoT-säkerhet](iot-security-best-practices.md) för mer information. | - |
| Bygg säker maskin vara | Använd säkerhetsfunktioner som krypterad lagring eller Trusted Platform Module (TPM) för att hålla enheterna och infrastrukturen säkrare. Se till att enhetens operativ system och driv rutiner har uppgraderats till de senaste versionerna, och om det är möjligt att installera antivirus program och program mot skadlig kod. Läs [IoT-säkerhetsarkitekturen](iot-security-architecture.md) för att förstå hur det kan hjälpa till att undvika flera säkerhetshot. | - |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Övervaka obehörig åtkomst till dina enheter |  Använd enhetens operativ Systems loggnings funktion för att övervaka säkerhets överträdelser eller fysisk manipulering av enheten eller dess portar. | - |
| Övervaka din IoT-lösning från molnet | Övervaka den övergripande hälsan för din IoT Hub lösning med hjälp av [måtten i Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Konfigurera diagnostik | Titta närmare på dina åtgärder genom att logga händelser i din lösning och skicka sedan diagnostikloggar till Azure Monitor för att få insyn i prestandan. Läs [övervaka och diagnostisera problem i IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) för mer information. | - |

## <a name="next-steps"></a>Nästa steg

För avancerade scenarier som involverar Azure IoT kan du behöva överväga ytterligare säkerhets krav. Mer information finns i [säkerhets arkitekturen för IoT](iot-security-architecture.md) .

