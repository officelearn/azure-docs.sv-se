---
title: Import Edge-paket för fjärrövervakningslösning – Azure | Microsoft-dokument
description: I den här artikeln beskrivs hur du importerar ett IoT Edge-paket till lösningsacceleratorn för fjärrövervakning
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61443019"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importera ett IoT Edge-paket till acceleratorn för fjärrövervakningslösningen

Ett distributionsmanifest definierar de moduler som ska distribueras till en IoT Edge-enhet. Den här artikeln förutsätter att en utvecklare i organisationen redan har skapat distributionsmanifest. Mer information om hur en utvecklare skapar ett manifest finns i någon av följande IoT Edge-instruktioner:

- [Distribuera Azure IoT Edge-moduler från Azure-portalen](../iot-edge/how-to-deploy-modules-portal.md)
- [Distribuera Azure IoT Edge-moduler med Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Distribuera Azure IoT Edge-moduler från Visual Studio-kod](../iot-edge/how-to-deploy-modules-vscode.md)

En utvecklare skapar och testar ett distributionsmanifest i en utvecklingsmiljö. När du är redo kan du importera manifestet till lösningsacceleratorn för fjärrövervakning.

## <a name="export-a-manifest"></a>Exportera ett manifest

Använd Azure-portalen för att exportera distributionsmanifestet från utvecklingsmiljön:

1. I Azure-portalen navigerar du till IoT-hubben som du använder för att utveckla och testa dina IoT Edge-enheter. Klicka på **IoT Edge** och sedan ![ **IoT Edge-distributioner:** IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Klicka på distributionen som har den distributionskonfiguration som du vill använda. Sidan **Distributionsinformation** visar: ![Distributionsinformation för IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Klicka på **Hämta IoT Edge-manifest:** ![Hämta distributionsmanifest](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Spara JSON-filen som en lokal fil som heter **deploymentmanifest.json**.

Nu har du en fil som innehåller distributionsmanifestet. I nästa avsnitt importerar du manifestet som ett paket till lösningen för fjärrövervakning.

## <a name="import-a-package"></a>Importera ett paket

Följ stegen nedan för att importera ett Edge-distributionsmanifest som ett paket till din lösning:

1. Navigera till sidan **Paket** i webbgränssnittet ![för fjärrövervakning: Paket](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Klicka på **+ Nytt paket**, välj **Kantmanifest** som pakettyp och klicka på **Bläddra** för att välja filen **deploymentmanifest.json** som du sparade i föregående avsnitt: ![Välj manifest](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Klicka på **Ladda upp** om du ![vill lägga till paketet i lösningen för fjärrövervakning: Laddat paket](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Du har nu laddat upp ett IoT Edge-distributionsmanifest som ett paket. På sidan **Distributioner** kan du distribuera det här paketet till dina anslutna IoT Edge-enheter.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar moduler till en IoT Edge-enhet från lösningen för fjärrövervakning är nästa steg att lära dig mer om [IoT Edge](../iot-edge/about-iot-edge.md).
