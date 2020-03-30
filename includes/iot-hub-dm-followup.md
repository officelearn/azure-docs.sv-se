---
title: ta med fil
description: ta med fil
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558783"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Anpassa och utöka enhetshanteringsåtgärderna

Dina IoT-lösningar kan utöka den definierade uppsättningen enhetshanteringsmönster eller aktivera anpassade mönster med hjälp av enhetstvilling- och moln-till-enhet-metodprimiver. Andra exempel på enhetshanteringsåtgärder är fabriksåterställning, uppdatering av den inbyggda programvaran, programuppdatering, energisparfunktioner, nätverks- och anslutningshantering samt datakryptering.

## <a name="device-maintenance-windows"></a>Fönster för enhetsunderhåll

Vanligtvis konfigurerar du enheter för att utföra åtgärder vid en tidpunkt som minimerar avbrott och driftstopp. Enhetsunderhållsfönster är ett vanligt mönster för att definiera när en enhet ska uppdatera konfigurationen. Backend-lösningarna kan använda de önskade egenskaperna för enhetstvillingen för att definiera och aktivera en princip på enheten som möjliggör ett underhållsfönster. När en enhet tar emot underhållsfönsterprincipen kan den använda den rapporterade egenskapen för enhetstvillingen för att rapportera principens status. Backend-appen kan sedan använda enhetstvillingfrågor för att intyga att enheter och varje princip följs.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du en direkt metod för att utlösa en fjärromstart på en enhet. Du använde de rapporterade egenskaperna för att rapportera den senaste omstartstiden från enheten och frågade enhetstvillingen för att upptäcka den senaste omstartstiden för enheten från molnet.

Information om hur du fortsätter att komma igång med IoT Hub och enhetshanteringsmönster som fjärröver uppdateringen av den inbyggda programvaran finns i [Så här gör du en uppdatering av den inbyggda programvaran](../articles/iot-hub/tutorial-firmware-update.md).

Mer information om hur du utökar IoT-lösningen och schemalägg metodanrop på flera enheter finns i [Schemalägga och sända jobb](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).