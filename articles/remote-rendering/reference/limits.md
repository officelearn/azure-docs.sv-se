---
title: Begränsningar
description: Kod begränsningar för SDK-funktioner
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: b6e501cdc1d5091a27d86406c6919587bedb261a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417699"
---
# <a name="limitations"></a>Begränsningar

Ett antal funktioner har storlek, antal eller andra begränsningar.

## <a name="azure-frontend"></a>Azure-frontend

* Totalt antal AzureFrontend-instanser per process: 16.
* Totalt antal AzureSession-instanser per AzureFrontend: 16.

## <a name="objects"></a>Objekt

* Totalt antal tillåtna objekt av en enskild typ (entitet, CutPlaneComponent osv.): 16 777 215.
* Totalt antal tillåtna aktiva klipp plan: 8.

## <a name="materials"></a>Material

* Totalt tillåtet material i en till gång: 65 535.

## <a name="overall-number-of-polygons"></a>Totalt antal polygoner

Det tillåtna antalet polygoner för alla laddade modeller beror på storleken på den virtuella datorn som skickas till [sessionen hanterings REST API](../how-tos/session-rest-api.md#create-a-session):

| Storlek på virtuell dator | Maximalt antal polygoner |
|:--------|:------------------|
|standard| 20 000 000 |
|denaturering| ingen gräns |


## <a name="platform-limitations"></a>Plattforms begränsningar

**Windows 10 Desktop**

* "Fristående PC"-distribution från Unity stöds inte. Använd UWP i stället.
* UWP/x86 är den enda UWP-plattform som stöds. UWP/x64 stöds inte.

**HoloLens 2**

* [Kamera funktionen rendera från PV](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) stöds inte.
