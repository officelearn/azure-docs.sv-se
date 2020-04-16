---
title: Begränsningar
description: Kodbegränsningar för SDK-funktioner
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417699"
---
# <a name="limitations"></a>Begränsningar

Ett antal funktioner har storlek, antal eller andra begränsningar.

## <a name="azure-frontend"></a>Azure Frontend

* Totalt AzureFrontend-instanser per process: 16.
* Totalt Antal AzureSession-instanser per AzureFrontend: 16.

## <a name="objects"></a>Objekt

* Totalt tillåtna objekt av en enda typ (Entitet, CutPlaneComponent, etc.): 16 777 215.
* Totalt tillåtna aktiva snittplan: 8.

## <a name="materials"></a>Material

* Totalt tillåtna material i en tillgång: 65 535.

## <a name="overall-number-of-polygons"></a>Totalt antal polygoner

Det tillåtna antalet polygoner för alla inlästa modeller beror på storleken på den virtuella datorn som skickas till [rest-API:et för sessionshantering:](../how-tos/session-rest-api.md#create-a-session)

| Storlek på virtuell dator | Maximalt antal polygoner |
|:--------|:------------------|
|standard| 20 miljoner |
|Premium| ingen gräns |


## <a name="platform-limitations"></a>Plattformsbegränsningar

**Windows 10-skrivbordet**

* Distributionen "Pc Standalone" från Unity stöds inte. Använd UWP istället.
* UWP/x86 är den enda UWP-plattformen som stöds. UWP/x64 stöds inte.

**Hololens 2**

* Återgivningen [från PV-kamerafunktionen](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) stöds inte.
