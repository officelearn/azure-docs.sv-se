---
title: Simulerad enhetsbeteende i lösning för simulering av enheter – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kan använda JavaScript för att definiera beteendet för en simulerad enhet i enheten simulering lösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b68550bce1f4e3fbe3845c21598720083c8e384c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285829"
---
# <a name="implement-the-device-model-behavior"></a>Implementera beteendet för enhetsmodellen

Artikeln [förstå schemat för enhetsmodellen](iot-accelerators-device-simulation-device-schema.md) beskrivs det schema som definierar en simulerad enhet-modell. Den här artikeln avses två typer av JavaScript-fil som implementerar beteendet för en simulerad enhet:

- **Tillstånd**: JavaScript-filer som körs med jämna tidsintervall att uppdatera det interna tillståndet för enheten.
- **Metoden**: JavaScript-filer som körs när lösningen anropar en metod på enheten.

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Kontrollera tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från IoT-hubb som den är ansluten till
> * Felsök ditt skript

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du definierar beteendet för dina egna anpassade simulerade enhetsmodell. Den här artikeln visar dig hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrollera tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från IoT-hubb som den är ansluten till
> * Felsök ditt skript

Nu när du har lärt dig hur du anger beteendet för en simulerad enhet, föreslagna nästa steg är att lära dig hur du [skapar en simulerad enhet](iot-accelerators-remote-monitoring-test.md).

Mer information för utvecklare om Enhetssimulering lösningen, se den [referensguide för utvecklare](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
