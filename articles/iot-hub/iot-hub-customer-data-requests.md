---
title: Kunddatabegäranden för Azure IoT Hub-enheter
description: De flesta enheter som hanteras i Azure IoT Hub är inte personliga, men vissa är det. I den här artikeln beskrivs administratörer som kan exportera eller ta bort personuppgifter från en enhet.
author: robinsh
ms.author: robinsh
ms.date: 05/16/2018
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: a05fbf6e1908f88014cd8da99fafb875de033f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499313"
---
# <a name="summary-of-customer-data-request-features"></a>Sammanfattning av funktionerna för kunddatabegäran

Azure IoT Hub är en REST API-baserad molntjänst som riktar sig till företagskunder som möjliggör säker dubbelriktad kommunikation mellan miljontals enheter och en partitionerad Azure-tjänst.

[!INCLUDE [gdpr-related guidance](../../includes/gdpr-intro-sentence.md)]

Enskilda enheter tilldelas en enhetsidentifierare (enhets-ID) av en klientadministratör. Enhetsdata baseras på det tilldelade enhets-ID:et. Microsoft har ingen information och har ingen åtkomst till data som gör att enhets-ID kan vara användarens korrelation.

Många av de enheter som hanteras i Azure IoT Hub är inte personliga enheter, till exempel en kontorstermostat eller fabriksrobot. Kunder kan dock betrakta vissa enheter som personligt identifierbara och efter eget gottfinnande kan behålla sina egna metoder för tillgång eller lagerspårning som binder enheter till individer. Azure IoT Hub hanterar och lagrar alla data som är associerade med enheter som om det vore personuppgifter.

Klientadministratörer kan använda antingen Azure-portalen eller tjänstens REST-API:er för att uppfylla informationsbegäranden genom att exportera eller ta bort data som är associerade med ett enhets-ID.

Om du använder routningsfunktionen i Azure IoT Hub-tjänsten för att vidarebefordra enhetsmeddelanden till andra tjänster, måste databegäranden utföras av klientadministratören för varje routningsslutpunkt för att slutföra en fullständig begäran om en viss enhet. Mer information finns i referensdokumentationen för varje slutpunkt. Mer information om slutpunkter som stöds finns i [Referens - IoT Hub-slutpunkter](iot-hub-devguide-endpoints.md).

Om du använder integrationsfunktionen i Azure Event Grid i Azure IoT Hub-tjänsten måste databegäranden utföras av klientadministratören för varje prenumerant av dessa händelser. Mer information finns i [Reagera på IoT Hub-händelser med hjälp av Event Grid](iot-hub-event-grid.md).

Om du använder Azure Monitor-integrationsfunktionen i Azure IoT Hub-tjänsten för att skapa diagnostikloggar, måste databegäranden utföras av klientadministratören mot de lagrade loggarna. Mer information finns [i Övervaka hälsotillståndet för Azure IoT Hub](iot-hub-monitor-resource-health.md).

## <a name="deleting-customer-data"></a>Ta bort kunddata

Klientadministratörer kan använda IoT-enhetsbladet för Azure IoT Hub-tillägget i Azure-portalen för att ta bort en enhet som tar bort data som är associerade med den enheten.

Det är också möjligt att utföra borttagningsåtgärder för enheter som använder REST API:er. Mer information finns i [Tjänst - Ta bort enhet](/rest/api/iothub/service/registrymanager/deletedevice).

## <a name="exporting-customer-data"></a>Exportera kunddata

Klientadministratörer kan använda kopiera och klistra in i IoT-enhetsfönstret i Azure IoT Hub-tillägget i Azure-portalen för att exportera data som är associerade med en enhet.

Det är också möjligt att utföra exportåtgärder för enheter som använder REST API:er. Mer information finns i [Service - Hämta enhet](/rest/api/iothub/service/registrymanager/getdevice).

> [!NOTE]
> När du använder Microsofts företagstjänster genererar Microsoft viss information, så kallade systemgenererade loggar. Vissa Azure IoT Hub-systemgenererade loggar är inte tillgängliga eller kan exporteras av klientadministratörer. Dessa loggar utgör faktiska åtgärder som utförs inom tjänsten och diagnostiska data relaterade till enskilda enheter.

## <a name="links-to-additional-documentation"></a>Länkar till ytterligare dokumentation

Fullständig dokumentation för Azure IoT Hub Service API:er finns på [IoT Hub Service API:er](https://docs.microsoft.com/rest/api/iothub/service/configuration).
