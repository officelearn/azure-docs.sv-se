---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b8d60da507aa1eb312d1a8505f756ff337085ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77592446"
---
I följande tabell visas de begränsningar som gäller för Azure IoT Hub Device Provisioning Service-resurser.

| Resurs | Gräns |
| --- | --- |
| Maximala enhetsetableringstjänster per Azure-prenumeration | 10 |
| Maximalt antal registreringar | 1,000,000 |
| Maximalt antal registreringar | 1,000,000 |
| Maximalt antal registreringsgrupper | 100 |
| Maximalt antal cas | 25 |
| Maximalt antal länkade IoT-hubbar | 50 |
| Maximal storlek på meddelandet | 96 KB|

> [!NOTE]
> Om du vill öka antalet registreringar och registreringar på etableringstjänsten kontaktar du [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!NOTE]
> Det går inte att öka det maximala antalet ca:er.

Enhetsetableringstjänsten begränsar begäranden när följande kvoter överskrids.

| Begränsning | Värde per enhet |
| --- | --- |
| Åtgärder | 200/min/service |
| Enhetsregistreringar | 200/min/service |
| Åtgärden för avsökning av enheter | 5/10 sek/enhet |
