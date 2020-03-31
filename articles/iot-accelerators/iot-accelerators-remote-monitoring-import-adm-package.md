---
title: Importpaket för fjärrövervakningslösning – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du importerar ett automatiskt enhetshanteringspaket till lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443453"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importera ett automatiskt enhetshanteringspaket till lösningsacceleratorn för fjärrövervakning

En automatisk enhetshanteringskonfiguration definierar konfigurationsändringarna för distribution till en grupp enheter. Den här artikeln förutsätter att en utvecklare i organisationen redan har skapat en automatisk enhetshanteringskonfiguration. Mer information om hur en utvecklare skapar en konfiguration finns i någon av följande IoT Hub-instruktioner:

- [Konfigurera och övervaka IoT-enheter i stor skala med Azure-portalen](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurera och övervaka IoT-enheter i stor skala med Hjälp av Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

En utvecklare skapar och testar en automatisk enhetshanteringskonfiguration i en utvecklingsmiljö. När du är redo kan du importera konfigurationen till lösningsacceleratorn för fjärrövervakning.

## <a name="export-a-configuration"></a>Exportera en konfiguration

Använd Azure-portalen för att exportera den automatiska enhetshanteringskonfigurationen från utvecklingsmiljön:

1. I Azure-portalen navigerar du till IoT-hubben som du använder för att utveckla och testa dina IoT-enheter. Klicka på **IoT-enhetskonfiguration:**

    [![Konfiguration av IoT-enhet](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klicka på den konfiguration som du vill använda. Sidan **Information om enhetskonfiguration** visas:

    [![Konfigurationsinformation för IoT-enheten](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klicka på **Hämta konfigurationsfil:**

    [![Hämta konfigurationsfil](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Spara JSON-filen som en lokal fil som heter **configuration.json**.

Nu har du en fil som innehåller den automatiska enhetshanteringskonfigurationen. I nästa avsnitt importerar du den här konfigurationen som ett paket till lösningen för fjärrövervakning.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera en automatisk enhetshanteringskonfiguration som ett paket till din lösning:

1. Navigera till sidan **Paket** i webbgränssnittet ![för fjärrövervakning: Paket](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klicka på **+ Nytt paket**, välj **Konfiguration** som pakettyp och klicka på **Bläddra** för att välja **filen configuration.json** som du sparade i föregående avsnitt:

    ![Välj konfiguration](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klicka på **Ladda upp** om du vill lägga till paketet i lösningen för fjärrövervakning:

    ![Uppladdat paket](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Du har nu laddat upp en automatisk enhetshanteringskonfiguration som ett paket. På sidan **Distributioner** kan du distribuera det här paketet till dina anslutna enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett konfigurationspaket och importerar det till lösningen för fjärrövervakning är nästa steg att lära dig hur du [hanterar enheter som är anslutna till fjärrövervakning i bulk](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
