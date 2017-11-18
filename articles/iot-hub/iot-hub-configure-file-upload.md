---
title: "Använda Azure portal för att konfigurera ladda upp filen | Microsoft Docs"
description: "Hur du använder Azure-portalen för att konfigurera din IoT-hubb för att aktivera filöverföringar från anslutna enheter. Innehåller information om hur du konfigurerar målet Azure storage-konto."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 2d875947297be5d47362369b96bc6ab0d90b3c93
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Konfigurera IoT-hubb filöverföringar med Azure-portalen

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Ladda upp filen

Att använda den [filen överför funktioner i IoT-hubb][lnk-upload], måste du först associera ett Azure Storage-konto med hubben. Välj **filuppladdning** att visa en lista över egenskaper för filöverföring för IoT-hubb som ändras.

![Visa inställningarna i portalen för IoT-hubb filöverföring][13]

**Lagringsbehållaren**: använda Azure portal för att markera en blob-behållare i ett Azure Storage-konto i din aktuella Azure-prenumeration ska associeras med din IoT-hubb. Om nödvändigt, du kan skapa ett Azure Storage-konto på den **lagringskonton** bladet och blob-behållaren på den **behållare** bladet. IoT-hubb genererar automatiskt SAS URI: er med behörighet att skriva till blob-behållare för enheter som ska användas när de överför filer.

![Visa behållare för lagring för filöverföring i portalen][14]

**Ta emot meddelanden för överförda filer**: aktivera eller inaktivera filen överför meddelanden via en alternativknapp.

**SAS TTL**: den här inställningen är den time-to-live SAS URI: er returneras till enheten av IoT-hubb. Som standard till en timme, men kan anpassas till andra värden med hjälp av skjutreglaget.

**Filen notification inställningar Standardbegränsning**: den time-to-live av en fil överför meddelande innan det förfaller. Ange en dag som standard men kan anpassas till andra värden med hjälp av skjutreglaget.

**Meddelande leverans av maximalt antal filer**: antalet gånger som IoT-hubben försöker att leverera en fil överför meddelande. Som standard till 10 men kan anpassas till andra värden med hjälp av skjutreglaget.

![Konfigurera filuppladdning för IoT-hubb i portalen][15]

## <a name="next-steps"></a>Nästa steg

Mer information om funktionerna som överför filen IoT-hubb finns [överföra filer från en enhet] [ lnk-upload] i utvecklarhandboken för IoT-hubb.

Du kan följa dessa länkar om du vill veta mer om hur du hanterar Azure IoT-hubb:

* [Massinläsning hantera IoT-enheter][lnk-bulk]
* [IoT-hubb mått][lnk-metrics]
* [Åtgärder som övervakning][lnk-monitor]

Om du vill utforska ytterligare funktionerna i IoT-hubb, se:

* [Utvecklarhandbok för IoT-hubb][lnk-devguide]
* [Distribuera AI till enheter med Azure IoT kant][lnk-iotedge]
* [Skydda din IoT-lösning från grunden upp][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
