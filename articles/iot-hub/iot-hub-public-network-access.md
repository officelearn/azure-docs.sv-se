---
title: Hantera offentlig nätverks åtkomst för Azure IoT Hub
description: Dokumentation om hur du inaktiverar och aktiverar offentlig nätverks åtkomst för IoT Hub
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937561"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>Hantera offentlig nätverks åtkomst för IoT Hub

Om du vill begränsa åtkomsten till enbart [privat slut punkt för din IoT Hub i ditt VNet](virtual-network-support.md)inaktiverar du åtkomst till offentliga nätverk. Det gör du genom att använda Azure Portal eller `publicNetworkAccess` API: et. 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Inaktivera offentlig nätverks åtkomst med Azure Portal

1. Besök [Azure Portal](https://portal.azure.com)
2. Gå till IoT-hubben.
3. Välj **nätverk** på menyn på den vänstra sidan.
4. Under Tillåt offentligt nätverk åtkomst till väljer du **inaktive rad**
5. Välj **Spara**.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="Bild som visar Azure Portal där du kan inaktivera offentlig nätverks åtkomst" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

Aktivera offentlig nätverks åtkomst genom att välja **aktive rad**och sedan **Spara**.

## <a name="ip-filter"></a>IP-filter 

Om offentlig nätverks åtkomst är inaktive rad ignoreras alla [IP-filter](iot-hub-ip-filtering.md) regler. Detta beror på att alla IP-adresser från det offentliga Internet blockeras. Använd alternativet **valda IP-intervall** om du vill använda IP-filter.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Fel korrigering med inbyggd Event Hub-kompatibel slut punkt

Det finns ett fel med IoT Hub där den [inbyggda Event Hub-kompatibla slut punkten](iot-hub-devguide-messages-read-builtin.md) fortsätter att vara tillgänglig via offentliga Internet när offentlig nätverks åtkomst till IoT Hub är inaktive rad. Mer information och kontakta oss om detta fel finns i [inaktivera offentlig nätverks åtkomst för IoT Hub inaktiverar åtkomst till den inbyggda slut punkten för Event Hub](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).