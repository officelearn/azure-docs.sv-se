---
title: Schemat för enheten i fjärrövervakningslösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver JSON-schemat som definierar en simulerad enhet i fjärrövervakningslösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c153153313511640f7969938f63ea9fbe7b0847c
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282659"
---
# <a name="understand-the-device-model-schema"></a>Förstå schemat för enhetsmodellen

Du kan använda simulerade enheter i lösningen för fjärrövervakning för att testa sitt beteende. När du distribuerar lösningen för fjärrövervakning kan etableras automatiskt en uppsättning simulerade enheter. Du kan anpassa befintliga simulerade enheter eller skapa egna.

Den här artikeln beskriver schemat för enhetsmodellen som anger funktioner och beteenden för en simulerad enhet. Enhetsmodell lagras i en JSON-fil.

I följande artiklar är relaterade till den aktuella artikeln:

* [Implementera beteendet för enhetsmodellen](iot-accelerators-remote-monitoring-device-behavior.md) beskriver JavaScript-filer som du använder för att implementera beteendet för en simulerad enhet.
* [Skapa en ny simulerad enhet](iot-accelerators-remote-monitoring-test.md) placerar den allt på samma plats och visar hur du distribuerar en ny typ av simulerad enhet i lösningen.

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

Nu när du har lärt dig om JSON-schemat, föreslagna nästa steg är att lära dig hur du [implementera beteendet för den simulerade enheten](iot-accelerators-remote-monitoring-device-behavior.md).

Mer information för utvecklare om lösningen för fjärrövervakning, finns:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
