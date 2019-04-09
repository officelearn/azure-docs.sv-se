---
title: Remote Monitoring solution Importera paket - Azure | Microsoft Docs
description: Den här artikeln beskrivs hur du importerar ett hanteringspaket för automatisk enheten till lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009860"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importera ett paket för hantering av enheter till lösningsacceleratorn för fjärrövervakning

En automatisk enhetshanteringskonfigurationen definierar konfigurationsändringar för att distribuera till en grupp av enheter. Den här artikeln förutsätter att en utvecklare i din organisation redan har skapat en automatisk enhetshanteringskonfigurationen. Mer information om hur utvecklare skapar en konfiguration, ser du något av följande instruktionsartiklar för IoT Hub:

- [Konfigurera och övervaka IoT-enheter i stor skala med Azure portal](../iot-hub/iot-hub-auto-device-config.md)
- [Konfigurera och övervaka IoT-enheter i stor skala med Azure CLI](../iot-hub/iot-hub-auto-device-config-cli.md)

En utvecklare som skapar och testar en automatisk enhetshanteringskonfigurationen i en utvecklingsmiljö. När du är klar kan importera du konfigurationen till lösningsacceleratorn för fjärrövervakning.

## <a name="export-a-configuration"></a>Exportera en konfiguration

Använd Azure-portalen för att exportera konfigurationen för en automatisk enheten från din utvecklingsmiljö:

1. Navigera till IoT-hubben som du använder för att utveckla och testa dina IoT-enheter i Azure-portalen. Klicka på **IoT enhetskonfiguration**:

    [![IoT enhetskonfiguration](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Klicka på den konfiguration som du vill använda. Den **information om enhetens konfiguration** visas:

    [![Ikonfigurationsdetaljer för oT enheten](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Klicka på **ladda ned konfigurationsfilen**:

    [![Dhämta konfigurationsfilen](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Spara JSON-filen som en lokal fil med namnet **configuration.json**.

Nu har du en fil som innehåller automatisk enhetshanteringskonfigurationen. I nästa avsnitt importerar du den här konfigurationen som ett paket till lösningen för fjärrövervakning.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera en automatisk enhetshanteringskonfigurationen som ett paket i din lösning:

1. Navigera till den **paket** sidan fjärrövervakning webbläsaren:  ![Paket-sidan](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Klicka på **+ nytt paket**, Välj **Configuration** som pakettyp och klicka på **Bläddra** att välja den **configuration.json** fil som du sparade i föregående avsnitt:

    ![Välj konfiguration](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Klicka på **överför** att lägga till paketet i lösningen för fjärrövervakning:

    ![Överförda paketet](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Nu har du laddat ner en automatisk enhetshanteringskonfigurationen som ett paket. På den **distributioner** kan du distribuera det här paketet till dina anslutna enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar ett konfigurationspaket och importerar den till från lösningen för fjärrövervakning, nästa steg är att lära dig hur du [hantera enheter som är anslutna till fjärrövervakning gruppvis](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
