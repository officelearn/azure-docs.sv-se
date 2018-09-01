---
title: Enheten schemat i lösning för simulering av enheter – Azure | Microsoft Docs
description: Den här artikeln beskriver JSON-schemat som definierar en simulerad enhet i enheten simulering lösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: b325873819caff139727ec15d6aecd2d4be89c9e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338163"
---
# <a name="understand-the-device-model-schema"></a>Förstå schemat för enhetsmodellen

Du kan använda lösningsaccelerator Enhetssimulering för att generera belastningar test för lösningar som använder IoT Hub. När du distribuerar lösningen Enhetssimulering kan etableras automatiskt en uppsättning simulerade enheter. Du kan anpassa befintliga simulerade enheter eller skapa egna.

Den här artikeln beskriver schemat för enhetsmodellen som anger funktioner och beteenden för en simulerad enhet. Enhetsmodell lagras i en JSON-fil.

I följande artiklar är relaterade till den aktuella artikeln:

* [Implementera beteendet för enhetsmodellen](iot-accelerators-device-simulation-device-behavior.md) beskriver JavaScript-filer som du använder för att implementera beteendet för en simulerad enhet.
* [Skapa en ny simulerad enhet](iot-accelerators-device-simulation-create-simulated-device.md) placerar den allt på samma plats och visar hur du distribuerar en ny typ av simulerad enhet i lösningen.

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhet-modell
> * Ange egenskaperna för den simulerade enheten
> * Ange den simulerade enheten skickar telemetri
> * Ange de metoder för moln-till-enhet som enheten svarar på

[!INCLUDE [iot-accelerators-device-schema](../../includes/iot-accelerators-device-schema.md)]

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du skapar dina egna anpassade simulerade enhetsmodell. Den här artikeln visar dig hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Använda en JSON-fil för att definiera en simulerad enhet-modell
> * Ange egenskaperna för den simulerade enheten
> * Ange den simulerade enheten skickar telemetri
> * Ange de metoder för moln-till-enhet som enheten svarar på

Nu när du har lärt dig om JSON-schemat, föreslagna nästa steg är att lära dig hur du [implementera beteendet för den simulerade enheten](iot-accelerators-device-simulation-device-behavior.md).

Mer information för utvecklare om Enhetssimulering lösningen, se den [referensguide för utvecklare](https://github.com/Azure/device-simulation-dotnet/wiki/Simulation-Service-Developer-Reference-Guide).
