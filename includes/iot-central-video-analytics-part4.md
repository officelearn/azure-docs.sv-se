---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: de916fcbe0623185821e2f5da15a8f9cf71dfd4e
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426792"
---
### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan lägga till en enhet i programmet måste du publicera enhets mal len:

1. I enhets mal len **lva Edge Gateway v2** väljer du **publicera**.

1. I **mallen publicera den här enheten på program** sidan väljer du **publicera**.

**Lva Edge Gateway v2** är nu tillgängligt som enhets typ som ska användas på sidan **enheter** i programmet.

## <a name="migrate-the-gateway-device"></a>Migrera Gateway-enheten

Den befintliga **gatewayen – 001-** enheten använder enhets mal len **lva Edge Gateway** . Migrera enheten till den nya enhets mal len om du vill använda ditt nya distributions manifest:

Så här migrerar du en **Gateway – 001-** enhet:

1. Gå till sidan **enheter** och välj den **Gateway-001-** enhet som ska markeras i listan.

1. Välj **Migrera**. Om ikonen **migrera** inte visas väljer du **...** om du vill se fler alternativ.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrera Gateway-enheten till en ny version":::

1. I listan i dialog rutan **migrera** väljer du **lva Edge Gateway v2** och väljer sedan **migrera**.

Efter några sekunder slutförs migreringen. Enheten använder nu **lva Edge Gateway v2** -enhets mal len med det anpassade distributions manifestet.

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

Du behöver de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enheter** väljer du **Gateway-001-** enheten.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** gör du en anteckning i *scratchpad.txt* -filen för **ID-omfånget** , **enhets-ID: t** och enhetens **primär nyckel**. Du använder dessa värden senare.

1. Se till att anslutnings metoden är inställd på **signatur för delad åtkomst**.

1. Välj **Stäng**.

## <a name="next-steps"></a>Nästa steg

Nu har du skapat ett IoT Central program med hjälp av program mal len **video analys – objekt och rörelse identifiering** , skapat en enhets mall för gateway-enheten och lagt till en gateway-enhet i programmet.

Om du vill testa programmet för video analys – objekt-och motion-identifiering med hjälp av IoT Edge moduler som kör en virtuell dator i molnet med simulerade video strömmar:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Om du vill prova program för video analys – objekt-och motion-identifiering med IoT Edge moduler som kör en riktig enhet med en riktig **ONVIF** -kamera:

> [!div class="nextstepaction"]
> [Skapa en IoT Edge-instans för video analys (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
