---
title: Enhets anslutning i Azure IoT Central | Microsoft Docs
description: Den här artikeln innehåller en översikt över hur Azure IoT Central hjälper dig att hålla enheterna anslutna och felfria.
author: aaronbjork
ms.author: abjork
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 23e9feea49f10a2fbc27961813cf02a4993467e9
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991994"
---
# <a name="stay-connected-with-azure-iot-central-preview-features"></a>Håll kontakten med Azure IoT Central (för hands versions funktioner)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Den här artikeln innehåller en översikt över hur Azure IoT Central hjälper enheterna att vara anslutna och felfria.

Med en IoT-lösning som är utformad för att användas i stor skala är det viktigt med en väl strukturerad metod för enhets hantering. Det är inte tillräckligt för att få enheter "anslutna" till den som kan, men du behöver en metod för att hålla enheterna anslutna och felfria när lösningen utvecklas, växer och åldras. Azure IoT Central tillhandahåller de funktioner som krävs för att säkerställa att enheterna i IoT-lösningen är väl vill vetaa för hela programmets livs cykel.

## <a name="dashboards"></a>Instrumentpaneler 
Inbyggda [instrument paneler](howto-manage-devices.md#import-devices) tillhandahåller ett anpassningsbart ytdiagram för att övervaka enhetens hälso tillstånd och telemetri. Börja med en fördefinierad instrument panel från en [Programmall](howto-use-app-templates-pnp.md) eller skapa egna instrument paneler som är anpassade efter behoven i ditt program. Instrument paneler kan delas med alla användare i ditt program eller vara privata.

## <a name="rules-and-actions"></a>Regler och åtgärder 
Identifiera snabbt enheter som behöver åtgärdas genom att skapa [anpassade regler](howto-create-event-rules.md) baserade på enhetens tillstånd och telemetri. Konfigurera [åtgärder](howto-create-event-rules.md#configure-actions) för att meddela rätt personer att se till att korrigerande åtgärder sker i rätt tid.

## <a name="jobs"></a>Jobb 
Med [jobb](howto-run-a-job.md) kan du utföra enstaka eller Mass uppdateringar av enhets egenskaper, inställningar och kommandon. 

## <a name="user-roles-and-permissions"></a>Användar roller och behörigheter
[Roller och behörigheter](howto-manage-users-roles-pnp.md) ger dig möjlighet att skräddarsy varje användares upplevelse. Skapa roller direkt via användar gränssnittet och tilldela lämpliga behörigheter. 




