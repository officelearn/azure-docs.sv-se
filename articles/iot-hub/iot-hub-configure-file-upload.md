---
title: Använd Azure-portalen för att konfigurera filöverföring | Microsoft-dokument
description: Så här använder du Azure-portalen för att konfigurera IoT-hubben för att aktivera filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar azure-lagringskontot för målet.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: bd7cc37b8fc81fc9d4109826743f2243913d0604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735053"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurera IoT Hub-filuppladdningar med Azure-portalen

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Ladda upp filer

Om du vill använda [filöverföringsfunktionen i IoT Hub](iot-hub-devguide-file-upload.md)måste du först associera ett Azure Storage-konto med din hubb. Välj **Filöverföring** om du vill visa en lista över filöverföringsegenskaper för IoT-hubben som ändras.

![Visa inställningar för uppladdning av IoT Hub-filer i portalen](./media/iot-hub-configure-file-upload/file-upload-settings.png)

* **Lagringsbehållare**: Använd Azure-portalen för att välja en blob-behållare i ett Azure Storage-konto i din aktuella Azure-prenumeration för att associera med din IoT Hub. Om det behövs kan du skapa ett Azure Storage-konto på **bladet Lagringskonton** och blob-behållaren på **bladet Behållare.** IoT Hub genererar automatiskt SAS-urier med skrivbehörighet till den här blob-behållaren för enheter som ska användas när de laddar upp filer.

   ![Visa lagringsbehållare för filöverföring i portalen](./media/iot-hub-configure-file-upload/file-upload-container-selection.png)

* **Ta emot meddelanden för uppladdade filer**: Aktivera eller inaktivera meddelanden om filuppladdning via växlingsknappen.

* **SAS TTL**: Den här inställningen är tid att leva på SAS-URI:er som returneras till enheten av IoT Hub. Ange till en timme som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Filmeddelandeinställningar standard TTL:** Tid till live för en filuppladdning meddelande innan den har löpt ut. Ange till en dag som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

* **Antal maximala leveransleveranser**för filmeddelanden : Antalet gånger IoT Hub försöker leverera ett meddelande om filöverföring. Ange till 10 som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

   ![Konfigurera IoT Hub-filöverföring i portalen](./media/iot-hub-configure-file-upload/file-upload-selected-container.png)

## <a name="next-steps"></a>Nästa steg

Mer information om filöverföringsfunktionerna för IoT Hub finns i [Ladda upp filer från en enhet](iot-hub-devguide-file-upload.md) i utvecklarhandboken för IoT Hub.

Följ de här länkarna om du vill veta mer om hur du hanterar Azure IoT Hub:

* [Masshantera IoT-enheter](iot-hub-bulk-identity-mgmt.md)
* [IoT Hub-mått](iot-hub-metrics.md)
* [Övervakning av åtgärder](iot-hub-operations-monitoring.md)

Mer information om hur du utforskar funktionerna i IoT Hub finns i:

* [Utvecklarhandledning för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Säkra din IoT-lösning från grunden](../iot-fundamentals/iot-security-ground-up.md)
