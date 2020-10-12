---
author: rothja
ms.service: iot-dps
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: bbe928084a758335e5fa3b0531726c77a6d599b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838881"
---
I följande tabell visas de begränsningar som gäller för Azure IoT Hub Device Provisioning Service-resurser.

| Resurs | Gräns |
| --- | --- |
| Maximalt antal enhets etablerings tjänster per Azure-prenumeration | 10 |
| Maximalt antal registreringar | 1,000,000 |
| Maximalt antal registreringar | 1,000,000 |
| Maximalt antal registrerings grupper | 100 |
| Maximalt antal ca: er | 25 |
| Maximalt antal länkade IoT-hubbar | 50 |
| Maximal meddelande storlek | 96 KB|

> [!NOTE]
> Kontakta [Microsoft Support](https://azure.microsoft.com/support/options/)om du vill öka antalet registreringar och registreringar på etablerings tjänsten.

> [!NOTE]
> Det finns inte stöd för att öka det maximala antalet ca: er.

Enhets etablerings tjänsten begränsar begär anden när följande kvoter överskrids.

| Begränsning | Värde per enhet |
| --- | --- |
| Åtgärder | 200/min/tjänst |
| Enhetsregistreringar | 200/min/tjänst |
| Enhets avsökning | 5/10 SEK/enhet |
