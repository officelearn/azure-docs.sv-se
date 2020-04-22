---
title: Säkerhetsrekommendationer för Azure IoT | Microsoft-dokument
description: I den här artikeln sammanfattas ytterligare steg för att säkerställa säkerheten i din Azure IoT Hub-lösning.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: 5dd09988d37982c41b761688492bd2dc3642b2db
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728985"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Säkerhetsrekommendationer för Azure Internet of Things (IoT) distribution

Den här artikeln innehåller säkerhetsrekommendationer för IoT. Genom att implementera dessa rekommendationer kan du uppfylla dina säkerhetsskyldigheter enligt beskrivningen i vår modell för delat ansvar. Mer information om vad Microsoft gör för att uppfylla tjänsteleverantörens ansvar finns i [Delat ansvar för molnbaserad databehandling](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91).

Några av rekommendationerna i den här artikeln kan övervakas automatiskt av Azure Security Center. Azure Security Center är den första försvarslinjen för att skydda dina resurser i Azure. Den analyserar regelbundet säkerhetstillståndet för dina Azure-resurser för att identifiera potentiella säkerhetsproblem. Den ger dig sedan rekommendationer om hur du ska hantera dem.

- Mer information om Azure Security Center-rekommendationer finns [i Säkerhetsrekommendationer i Azure Security Center](../security-center/security-center-recommendations.md).
- Information om Azure Security Center finns i [Vad är Azure Security Center?](../security-center/security-center-intro.md)

## <a name="general"></a>Allmänt

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar, programmeringsspråk, protokoll och ramverk som stöds. | - |
| Skydda autentiseringsnycklar | Håll enhets-ID:n och deras autentiseringsnycklar fysiskt säkra efter distributionen. På så sätt undviks en skadlig enhet som en registrerad enhet. | - |
| Använd enhetSDK:er när det är möjligt | Enhets-SDK:er implementerar en mängd olika säkerhetsfunktioner, till exempel kryptering, autentisering och så vidare, för att hjälpa dig att utveckla ett robust och säkert enhetsprogram. Mer information finns i [Förstå och använda Azure IoT Hub SDK:er.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) | - |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering 

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Definiera åtkomstkontroll för navet | [Förstå och definiera vilken typ av åtkomst](iot-security-deployment.md#securing-the-cloud) varje komponent kommer att ha i din IoT Hub-lösning, baserat på funktionaliteten. De tillåtna behörigheterna är *Register Read,* *RegistryReadWrite*, *ServiceConnect*och *DeviceConnect*. Standardprinciper för [delad åtkomst i IoT-hubben](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan också hjälpa dig att definiera behörigheterna för varje komponent baserat på dess roll. | - |
| Definiera åtkomstkontroll för backend-tjänster | Data som används av din IoT Hub-lösning kan förbrukas av andra Azure-tjänster som [Cosmos DB,](https://docs.microsoft.com/azure/cosmos-db/) [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [App Service,](https://docs.microsoft.com/azure/app-service/) [Logic Apps](https://docs.microsoft.com/azure/logic-apps/)och [Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Se till att förstå och tillåta lämpliga åtkomstbehörigheter som dokumenterats för dessa tjänster. | - |

## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Säker enhetsautentisering | Säkerställ säker kommunikation mellan dina enheter och din IoT-hubb genom att använda antingen [en unik identitetsnyckel eller säkerhetstoken](iot-security-deployment.md#iot-hub-security-tokens)eller [ett X.509-certifikat på enheten](iot-security-deployment.md#x509-certificate-based-device-authentication) för varje enhet. Använd lämplig metod för att [använda säkerhetstoken baserat på det valda protokollet (MQTT, AMQP eller HTTPS).](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security) | - |
| Säker enhetskommunikation | IoT Hub säkrar anslutningen till enheterna med TLS-standarden (Transport Layer Security), med stöd för versionerna 1.2 och 1.0. Använd [TLS 1.2](https://tools.ietf.org/html/rfc5246) för att säkerställa maximal säkerhet. | - |
| Säker servicekommunikation | IoT Hub tillhandahåller slutpunkter för att ansluta till backend-tjänster som [Azure Storage](/azure/storage/) eller [Event Hubs](/azure/event-hubs) med endast TLS-protokollet och ingen slutpunkt visas på en okrypterad kanal. När dessa data når dessa backend-tjänster för lagring eller analys, se till att använda lämpliga säkerhets- och krypteringsmetoder för den tjänsten och skydda känslig information i backend. | - |

## <a name="networking"></a>Nätverk

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Skydda åtkomsten till dina enheter | Håll maskinvaruportar i dina enheter till ett minimum för att undvika oönskad åtkomst. Dessutom bygga mekanismer för att förhindra eller upptäcka fysisk manipulering av enheten. Läs metodtips för [IoT-säkerhet](iot-security-best-practices.md) för mer information. | - |
| Skapa säker maskinvara | Införliva säkerhetsfunktioner som krypterad lagring eller TPM (Trusted Platform Module) för att skydda enheter och infrastruktur. Håll enhetens operativsystem och drivrutiner uppgraderade till de senaste versionerna, och om utrymmet tillåter, installera antivirus- och antimalware-funktioner. Läs [IoT-säkerhetsarkitektur](iot-security-architecture.md) för att förstå hur detta kan bidra till att minska flera säkerhetshot. | - |

## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentarer | Stöds av ASC |
|-|----|--|
| Övervaka obehörig åtkomst till dina enheter |  Använd enhetens loggningsfunktion för att övervaka eventuella säkerhetsöverträdelser eller fysisk manipulering av enheten eller dess portar. | - |
| Övervaka din IoT-lösning från molnet | Övervaka den övergripande hälsan för din IoT Hub-lösning med hjälp av [måtten i Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). | - |
| Konfigurera diagnostik | Titta noga på dina åtgärder genom att logga händelser i din lösning och sedan skicka diagnostikloggarna till Azure Monitor för att få insyn i prestanda. Läs [Övervaka och diagnostisera problem i din IoT-hubb](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) för mer information. | - |

## <a name="next-steps"></a>Nästa steg

För avancerade scenarier som involverar Azure IoT kan du behöva överväga ytterligare säkerhetskrav. Mer vägledning finns i [IoT-säkerhetsarkitektur.](iot-security-architecture.md)

