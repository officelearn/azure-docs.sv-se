---
title: Använda Azure portal för att konfigurera filuppladdning | Microsoft Docs
description: Hur du använder Azure-portalen för att konfigurera IoT hub för att aktivera filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar målet Azure storage-konto.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: a9f9eeaed2716c5d492099568fd6f90080471af2
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42060925"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurera IoT Hub-filöverföringar med Azure portal

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Filuppladdning

Du använder den [filen filuppladdning i IoT Hub](iot-hub-devguide-file-upload.md), måste du först associera ett Azure Storage-konto med hubben. Välj **filuppladdning** att visa en lista över egenskaper för filöverföring för IoT-hubben som ändras.

![Visa inställningarna i portalen för IoT Hub filöverföring](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Lagringsbehållare**: Använd Azure-portalen för att välja en blob-behållare i ett Azure Storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Om behövs kan du skapa ett Azure Storage-konto på den **lagringskonton** bladet och blob-behållare på den **behållare** bladet. IoT Hub genererar automatiskt SAS URI: er med skrivbehörighet till den här blob-behållare för enheter som ska användas när de laddar upp filer.

   ![Visa storage-behållare för filöverföring i portalen](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Ta emot meddelanden om uppladdade filer**: aktivera eller inaktivera filen ladda upp meddelanden via växlingsknappen.

* **SAS TTL**: den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT Hub. Ange en timme som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Filen notification inställningar standard TTL**: den time-to-live för en fil ladda upp meddelandet innan det förfaller. Inställd på en dag som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Filen notification maximalt antal leveranser**: antalet gånger som IoT-hubben försöker leverera en fil ladda upp meddelandet. Som standard har angetts till 10 men kan anpassas till andra värden med hjälp av skjutreglaget.

   ![Konfigurera filuppladdning för IoT-hubb i portalen](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om funktionerna du kan ladda upp filen för IoT Hub, [ladda upp filer från en enhet](iot-hub-devguide-file-upload.md) i utvecklarhandboken för IoT Hub.

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Skydda din IoT-lösning från grunden upp](../iot-fundamentals/iot-security-ground-up.md)
