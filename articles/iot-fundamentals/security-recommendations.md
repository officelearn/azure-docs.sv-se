---
title: Säkerhetsrekommendationer för Azure IoT | Microsoft Docs
description: Den här artikeln sammanfattas ytterligare steg för att garantera säkerheten i din Azure IoT Hub-lösning.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722867"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Säkerhetsrekommendationer för distribution av Azure Internet of Things (IoT)

Den här artikeln innehåller säkerhetsrekommendationer för Azure IoT-tjänster. Implementera de här rekommendationerna kommer hjälp du uppfylla dina security skyldigheter som kund av Azure IoT och kommer att förbättra den övergripande säkerheten för dina IoT-lösningar. Mer information om de inbyggda säkerhetsfunktioner som tillhandahålls av Azure IoT [IoT-säkerhet från grunden](iot-security-ground-up.md).

## <a name="general"></a>Allmänt

| Rekommendation | Kommentar |
|-|-|
| Håll dig uppdaterad | Använd de senaste versionerna av plattformar som stöds, programmeringsspråk, protokoll och ramverk. |
| Skydda autentiseringsnycklar | Skydda enhets-ID och deras autentiseringsnycklar fysiskt efter distributionen. En obehörig enhet maskerade på så sätt undviker som en registrerad enhet. |
| Använd SDK: er enheter när det är möjligt | SDK: er för enhetens implementera en mängd säkerhetsfunktioner, till exempel kryptering, autentisering och så vidare, som hjälper dig att utveckla ett enhetprogram med robusta och säkra. Se [förstå och använda Azure IoT Hub SDK: er](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) för mer information. |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Rekommendation | Kommentar |
|-|-|
| Definiera åtkomstkontroll för hubben | [Förstå och definiera vilken typ av åtkomst](iot-security-deployment.md#securing-the-cloud) varje komponent har i din IoT Hub-lösning baserad på funktionen. Tillåtna behörigheter är *registret Läs*, *RegistryReadWrite*, *ServiceConnect*, och *DeviceConnect*. Standard [delade åtkomstprinciper i IoT hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) kan också definiera behörigheter för varje komponent baserat på dess roll. |
| Definiera åtkomstkontroll för backend-tjänster | Data som matas in i din IoT Hub-lösning kan användas av andra Azure-tjänster som [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [Apptjänst](https://docs.microsoft.com/azure/app-service/), [Logikappar](https://docs.microsoft.com/azure/logic-apps/), och [Blob-lagring](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Se till att förstå och låta rätt behörigheter enligt beskrivningen i dessa tjänster. |


## <a name="data-protection"></a>Dataskydd

| Rekommendation | Kommentar |
|-|-|
| Säker autentisering | Se till att säker kommunikation mellan dina enheter och din IoT-hubb genom att använda antingen [en unik identitet nyckel eller security token](iot-security-deployment.md#iot-hub-security-tokens), eller [ett X.509-certifikat på enheten](iot-security-deployment.md#x509-certificate-based-device-authentication) för varje enhet. Använd lämplig metod att [använder säkerhetstoken baserat på det valda protokollet (MQTT-, AMQP- eller HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Säker kommunikation | IoT Hub skyddar anslutningen till de enheter som använder Transport Layer Security (TLS)-standard, stöd för version 1.2, 1.2 och 1.0. Använd [TLS 1.2](https://tools.ietf.org/html/rfc5246) att säkerställa maximal säkerhet. |
| Säker kommunikation | IoT Hub innehåller slutpunkter för att ansluta till serverdelstjänster som [Azure Storage](/azure/storage/) eller [Händelsehubbar](/azure/event-hubs) med hjälp av endast TLS-protokollet och ingen slutpunkt är exponerad på en okrypterad kanal. När dessa data når dessa serverdelstjänster för lagring och analys, se till att använda lämpliga metoder för tjänsten som säkerhet och kryptering och skydda känslig information i serverdelen. |


## <a name="networking"></a>Nätverk

| Rekommendation | Kommentar |
|-|-|
| Skydda åtkomsten till dina enheter | Behåll maskinvara portar i dina enheter till ett absolut minsta krav att undvika oönskad åtkomst. Dessutom skapa mekanismer för att förhindra eller upptäcka fysiska manipulering av enheten. Läs [IoT säkerhetsmetoder](iot-security-best-practices.md) mer information. |
| Skapa säker maskinvara | Lägga till säkerhetsfunktioner som krypterad lagring eller Trusted Platform Module (TPM), för att skydda enheterna och infrastrukturen. Se till att enhetens operativsystem och drivrutiner som uppgraderas till de senaste versionerna och om utrymmet tillåter installera funktioner för virus och skadlig kod. Läs [IoT Säkerhetsarkitektur](iot-security-architecture.md) att förstå hur detta kan hjälpa att undvika flera säkerhetshot. |


## <a name="monitoring"></a>Övervakning

| Rekommendation | Kommentar |
|-|-|
| Övervaka dina enheter från obehörig åtkomst |  Använda funktionen för användningsloggning för ditt operativsystem för enheten för att övervaka eventuella säkerhetsbrister eller fysiska manipulation av enheten eller dess portar. |
| Övervaka din IoT-lösning från molnet | Övervaka den övergripande hälsan för din IoT Hub-lösning med den [mått i Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Konfigurera diagnostik | Titta noga på din verksamhet genom att logga händelser i din lösning och sedan skicka diagnostiska loggar till Azure Monitor för att få insyn i prestanda. Läs [övervaka och diagnostisera problem i din IoT-hubb](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) för mer information. |

## <a name="next-steps"></a>Nästa steg

Du kan behöva tänka på ytterligare säkerhetskrav för avancerade scenarier som rör Azure IoT. Se [IoT Säkerhetsarkitektur](iot-security-architecture.md) för mer information.

