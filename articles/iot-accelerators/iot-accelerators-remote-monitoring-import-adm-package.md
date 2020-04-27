---
title: Import paket för fjärrövervakning – Azure | Microsoft Docs
description: I den här artikeln beskrivs hur du importerar ett automatiskt enhets hanterings paket till den fjärrstyrda lösnings acceleratorn
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "61443453"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importera ett automatiskt enhets hanterings paket till din lösnings Accelerator för fjärr styrning

En automatisk enhets hanterings konfiguration definierar de konfigurations ändringar som ska distribueras till en grupp av enheter. Den här artikeln förutsätter att en utvecklare i din organisation redan har skapat en automatisk enhets hanterings konfiguration. Information om hur en utvecklare skapar en konfiguration finns i något av följande IoT Hub instruktions artiklar:

- [Konfigurera och övervaka IoT-enheter i skala med hjälp av Azure Portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurera och övervaka IoT-enheter i skala med Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

En utvecklare skapar och testar en automatisk enhets hanterings konfiguration i en utvecklings miljö. När du är klar kan du importera konfigurationen till din lösnings Accelerator för fjärr styrning.

## <a name="export-a-configuration"></a>Exportera en konfiguration

Använd Azure Portal för att exportera den automatiska enhets hanterings konfigurationen från utvecklings miljön:

1. I Azure Portal navigerar du till IoT-hubben som du använder för att utveckla och testa dina IoT-enheter. Klicka på **IoT-enhets konfiguration**:

    [![IoT-enhets konfiguration](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klicka på den konfiguration som du vill använda. Sidan **enhets konfigurations information** visar:

    [![Konfigurations information för IoT-enheter](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klicka på **Hämta konfigurations fil**:

    [![Ladda ned konfigurations fil](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Spara JSON-filen som en lokal fil med namnet **Configuration. JSON**.

Nu har du en fil som innehåller konfigurationen för automatisk enhets hantering. I nästa avsnitt importerar du den här konfigurationen som ett paket i lösningen för fjärrövervakning.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera en automatisk enhets hanterings konfiguration som ett paket till din lösning:

1. Gå till sidan **paket** på sidan för webb gränssnitt för fjärrövervakning ![: Sidan paket](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klicka på **+ nytt paket**, Välj **konfiguration** som paket typ och klicka på **Bläddra** för att välja den **Configuration. JSON** -fil som du sparade i föregående avsnitt:

    ![Välj konfiguration](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klicka på **överför** för att lägga till paketet i lösningen för fjärrövervakning:

    ![Överfört paket](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Du har nu laddat upp en automatisk enhets hanterings konfiguration som ett paket. På sidan **distributioner** kan du distribuera det här paketet till dina anslutna enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett konfigurations paket och importerar det till från lösningen för fjärrövervakning är nästa steg att lära dig hur du [hanterar enheter som är anslutna till fjärrövervakning i bulk](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
