---
title: Enhetssimulering med IoT fjärrövervakning – Azure | Microsoft Docs
description: Den här guiden visar hur du använder enhetssimulatorn med den lösningsacceleratorn för fjärrövervakningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 81efd9dc5d33ed23574b1cb66f26ccb444d5a3ff
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180542"
---
# <a name="create-and-test-a-new-simulated-device"></a>Skapa och testa en ny simulerad enhet

Lösningsacceleratorn för fjärrövervakning kan du definiera egna simulerade enheter. Den här artikeln visar hur du definierar en ny simulerad glödlampan-enhet och sedan testa den lokalt. Solution accelerator omfattar simulerade enheter, till exempel chillers och lastbilar. Du kan också definiera egna simulerade enheter för att testa dina IoT-lösningar innan du distribuerar verkliga enheter.

> [!NOTE]
> Den här artikeln beskriver hur du använder simulerade enheter i device simulering-tjänsten. Om du vill skapa en riktig enhet Se [ansluta enheten till lösningsacceleratorn för fjärrövervakning](iot-accelerators-connecting-devices.md).

Den här guiden visar hur du anpassar enheten simulering mikrotjänst. Den här mikrotjänst är en del av lösningsacceleratorn för fjärrövervakning. För att visa enheten simuleringsfunktioner, använder i den här guiden två scenarier i Contoso-IoT-program:

[!INCLUDE [iot-solution-accelerators-create-device](../../includes/iot-solution-accelerators-create-device.md)]

## <a name="next-steps"></a>Nästa steg

Den här guiden visar hur du skapar en anpassad simulerad enhet typer och testa dem genom att köra enheten simulering mikrotjänster lokalt.

Föreslagna nästa steg är att lära dig hur du distribuerar din anpassade simulerade enhetstyper till den [lösningsacceleratorn för fjärrövervakning](iot-accelerators-remote-monitoring-deploy-simulated-device.md).
